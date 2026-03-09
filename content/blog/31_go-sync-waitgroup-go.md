---
title: 'New addition to the Go sync package since Go v1.25'
url: '/blog/sync-waitgroup-go'
date: Sat, 07 Mar 2026 19:00:00 +0000
draft: false
tags: [golang, short]
featured: false
toc: false
summary: 'I am discovering a new addition to the sync package: sync.WaitGroup.Go()'
---

Something I missed previously when reading the Go 1.25 release note in the ["minor changes to the library"](https://go.dev/doc/go1.25#minor_library_changes) section: an entry for the [sync package](https://go.dev/doc/go1.25#syncpkgsync):

> sync
> 
> The new WaitGroup.Go method makes the common pattern of creating and counting Goroutines more convenient.

It's a small improvement that makes it harder to make mistakes so this is more than just convenience! Let's see how it looks.

## Example

Below I am assuming that you are already slightly familiar with Go concurrency and the `sync` package in the Go standard library. If not, you should start there first :)

### The "classic" way: `sync.WaitGroup.Add()` + `defer sync.WaitGroup.Done()`

Before Go 1.25, the way to use `sync.WaitGroup` to count Goroutines was as follow:

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	var wg sync.WaitGroup
	wg.Add(2)
	go func() {
		defer wg.Done()
		run(1, "A")
	}()
	go func() {
		defer wg.Done()
		run(5, "B")
	}()
	wg.Wait()
}

func run(iter int, name string) {
	for i := 0; i < iter; i++ {
		time.Sleep(time.Second)
		fmt.Println(name)
	}
}
```
The example is borrowed from Rob Reid's blog post _runtime.Goexit_[^1] where he discusses a few alternatives way to implement this pattern, with the tools available in 2018.

One additional note: this code is assuming we don't have access to the `run` function.

### The "new" way: `sync.WaitGroup.Go()`

Rewriting the previous example for Go v1.25 and newer versions. This is also now the preferred way to use `sync.WaitGroup` according to the docs for [Add](https://pkg.go.dev/sync#WaitGroup.Add) and [Done](https://pkg.go.dev/sync#WaitGroup.Done)

> Callers should prefer [WaitGroup.Go]

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	var wg sync.WaitGroup
	wg.Go(func() {
		run(1, "A")
	}())
	wg.Go(func() {
		run(5, "B")
	}())
	wg.Wait()
}

func run(iter int, name string) {
	for i := 0; i < iter; i++ {
		time.Sleep(time.Second)
		fmt.Println(name)
	}
}
```

## Comparison

The good things
- we can get rid of the manual counting with `sync.WaitGroup.Add(n)` in the main Goroutine, this is automatically handled for you.
- we can remove the call to `defer sync.WaitGroup.Done()` in each of the Goroutines, this is automatically handled for you.
- the code looks the same wether we have access to the `run` function or not.

The negative I think is that by not using the keyword `go` directly, but instead using a normal library function that wraps it, when quickly looking over the code (like diagonal reading), you might miss the Goroutine creation since we are so used to see the `go` keyword highlighted in our favorite IDE. Would that be enough to not use it though? I don't think so!

## The end

A few more links to stay on the topic of Go concurrency

- [https://robreid.io](https://robreid.io) has some post about Golang concurrency patterns
- [Implementation of sync.WaitGroup.Go()](https://cs.opensource.google/go/go/+/refs/tags/go1.26.1:src/sync/waitgroup.go;l=236) to see how it's implemented. Nothing fancy to see here but it is always interesting to peek.
- A much more interesting function to look at is [sync.WaitGroup.Add() in Go v1.24](https://cs.opensource.google/go/go/+/refs/tags/go1.24.9:src/sync/waitgroup.go;l=45) and its subsequent evolution [sync.WaitGroup.Add() in Go v1.25+](https://cs.opensource.google/go/go/+/refs/tags/go1.25.0:src/sync/waitgroup.go;l=77) with the introduction of the [test/synctest package](https://pkg.go.dev/testing/synctest) to test concurrent/asynchronous code.
- [Go Concurrency Paterns: Pipelines and cancellation](https://go.dev/blog/pipelines) from the Go blog
- [Gist of Go concurrency](https://antonz.org/go-concurrency/) book, available online freely.

[^1]: [runtime.Goexit by Rob Reid(2018)](https://robreid.io/runtime-goexit/)
