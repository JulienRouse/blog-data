---
title: 'Print the Go syntax representation of a value'
url: '/blog/print-the-go-syntax-representation-of-a-value'
date: Thu, 15 Feb 2024 19:00:00 +0000
draft: false
tags: [golang, short]
featured: false
toc: false
summary: 'Discovering new use of Golang formatted string'
---


I learned a new trick: `fmt.Sprintf` (and `fmt.Printf`) support a format verb `%#v` that prints out a "Go-syntax representation of the value" (quoting the documentation).

What this mean: you can ouput a value the way you would write it in valid Go source code.

```Go
package main

import "fmt"

func main() {

	var s = []string{"string in the slice"}

	fmt.Printf("%v\n", s) // output [string in the slice]
	fmt.Printf("%#v\n", s) // output: []string{"string in the slice"}
	// To output only the type, you can use `%T` instead
	fmt.Printf("%T\n", s) // output: []string
}
```

## Why is it useful?

For me it was useful when using Go template to generate Go code (generating CLI commands from OpenAPI specification).

In my input file, I had a slice I wanted to insert into the generated code, but I can't use the value directly, because it's not valid Go code. There is a few workaround (like iterating on the slice to output each value inside and reconstructing the slice) but using `%#v` is by far the best approach.


## References

- Documentation for [text/template](https://pkg.go.dev/text/template)
- Documentation for [fmt](https://pkg.go.dev/fmt)