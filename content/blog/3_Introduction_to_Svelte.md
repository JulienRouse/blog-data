---
title: 'Introduction to Svelte'
url: '/blog/introduction-to-svelte'
date: Sat, 08 Jun 2019 19:00:00 +0000
draft: false
tags: [svelte,SPA]
featured: true
toc: true
summary: "Let's discover Svelte and build a little idle game together."
---

# Introduction to Svelte

I wanted to try out Svelte[^1] so I made a little clone of a game called Derivative Clicker[^2]. Following is a very quick presentation of what is Svelte (and how it compares to other reactive framework like React[^3], Angular[^4], etc) then a multi part tutorial on how to make a little game app with Svelte. 

- [introduction to Svelte]({{< relref "3_Introduction_to_Svelte.md" >}})
- [part1: Start of the tutorial: making a Derivative Idle clone with Svelte]({{< relref "3_Tutorial_for_a_Derivative_Clicker_clone_with_Svelte.md" >}})
- **part2: Going further with Svelte using components and props**: Coming soon

## Svelte

Svelte is in competition with React, Angular and other framework to build  reactive Single Page Application, but the difference is Svelte shifts the work from the browser to the compiler. It still delivers Reactive Single page application but with a very reduced size. Similar initiative can be found as well with Aurelia[^5].

The project went to version 3 a month ago (early may 2019), I think it's a good time to try it out :)

### PROS

The Svelte tutorial[^6] is great, it takes about an hour or so and is very well done. It uses the Svelte REPL[^7] and it introduces concepts very smoothly. Also it uses mostly the same concepts as React, Vue[^8] and Angular. If you know at least one of those you should pick it up quickly. (Component, reactive bindings, props, event forwarding, state store, component life cycle and slots to name a few concepts used) .

Also the Svelte website features a very nice REPL that you can use to test Svelte app without installing anything. When you are ready for the next step, the *get started section*[^9] is handy. It's just a matter of cloning a template from a git[^10] repository and use the tools you are used to: `npm`[^11] or `yarn`[^12], `webpack`[^13], `rollup`[^14] or `browserify`[^15]. And for the deployment, there are instructions a quick setup with Now[^16] and Surge[^17] or you can choose you favorite deployment option as you would for any other JS or TypeScript project.

Another pro, look at this article for a [RealWorld benchmark](https://medium.freecodecamp.org/a-realworld-comparison-of-front-end-frameworks-with-benchmarks-2019-update-4be0d3c78075) to get a feel of how Svelte can be very slim compared to its rivals. (Note that the benchmark was done with Svelte v2, and right now it is already Svelte v3, so take it with a grain of salt).

### CONS

I think it got traction recently but it still is not widely used so there not much educational material yet. If you can't do with only the official tutorial and API documentation, you should wait a bit more.

Also Svelte miss features like routing (but this is apparently addressed in the sister framework Sapper[^18] which aims to be a replacement for Next.js[^19]).

And last but not least, it has some weird syntax choices. The dollar sign `$` has two different uses dependent on the context. 

```js
let count = 0;
$: doubled = count * 2; // here doubled is a 'derived' value on 'count', it will automatically recalculate itself if count changes
```

```html
<p> My name is {$name} </p> 
<!-- Here $name is a reference to name, a value in a state store -->
```

I don't like that choice. Note also that the second syntax is a shorthand syntax, you can subscribe manually to values in the store and name them without the `$` so if you really care about that you can opt out (at the price of 3-4 lines of manual wiring).

And syntax for conditional in template is also not very sexy: 

```html
{{ #if condition }}
 <p> Inside the if </p>
{{ /if}}  
```

But I guess it's more a matter of personal taste. 

## Practicing Svelte by building a game

To continue onto the first part of the tutorial you can follow this link: [Start of the tutorial: making a Derivative Idle clone with Svelte]({{< relref "3_Tutorial_for_a_Derivative_Clicker_clone_with_Svelte.md" >}})

[^1]: https://svelte.dev/
[^2]: http://gzgreg.github.io/DerivativeClicker/
[^3]: https://reactjs.org/
[^4]: https://angular.io/
[^5]: https://aurelia.io/
[^6]: https://svelte.dev/tutorial/basics
[^7]: https://svelte.dev/repl/hello-world?version=3.5.1
[^8]: https://vuejs.org/
[^9]: https://svelte.dev/blog/the-easiest-way-to-get-started
[^10]: https://git-scm.com/
[^11]: https://www.npmjs.com/
[^12]: https://yarnpkg.com/en/
[^13]: https://webpack.js.org/
[^14]: https://rollupjs.org/guide/en/
[^15]: http://browserify.org/
[^16]: https://zeit.co/now
[^17]: https://surge.sh/
[^18]: https://sapper.svelte.dev/
[^19]: https://github.com/zeit/next.js