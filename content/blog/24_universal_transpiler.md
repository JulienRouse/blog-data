---
title: 'Looking around for the universal transpiler'
url: '/blog/looking-around-for-the-universal-transpiler'
date: Sat, 30 Oct 2021 19:00:00 +0000
draft: true
tags: [compilers, interpreters, transpilers]
featured: false
toc: false
summary: 'What if we could transpile every programming language to every other one?'
---

Lately I have a question in mind that revolve around compilation, and that is why in the [last post](23_Ressources_to_understand_compilers_and_interpreters.md) I was hoarding resources to know more about compilers, interpreters and transpilers.

The question is: would it be possible to have an universal transpiler?

## Transpilers?

Sometimes called *source-to-source compiler*, transpiler are specialized form of compiler that take source code as input and output source code from a different language. Most commonly, we see this with language that target Javascript like ClojureScript, TypeScript but also tools like [Babel](https://babeljs.io/docs/en/) that compile ECMAScript 2015+ code into backward compatible version of JavaScript, or [Pandoc](https://pandoc.org/) that transform document from (and to) format like .docx, .md, .org, EPUB, LaTeX, CSV, PDF and a lot more!


## Previous work

### Hy <--> Python transformation

https://brandonwillard.github.io/readable-strings-and-relational-programming-in-hy.html

[Hy docs](https://docs.hylang.org/en/alpha/)




### Universal transpiler

http://jarble.github.io/transpiler/

### Lux programming language

A work-in-progress programming language that is built with the idea in mind to be run on many platform including the JVM, JavaScript, Python, Lua and Ruby.


### [Pandoc](https://pandoc.org/)

Not exactly a transpiler in the strictest sense maybe, but Pandoc is a tool that convert from and to various documents formats.



## Appendix: what I understand by "compiler", "interpreter" and "transpiler"

Below is how I see those different concepts. You can have a better explanation in the book "Crafting interpreters" that has a free version online, I link to each section below:

[compiler](http://craftinginterpreters.com/a-map-of-the-territory.html#compilers-and-interpreters): a program that takes some input (usually source code) and transform it into something else. An example of that is gcc, one C compiler. It takes C source code, and output something that is usually a binary (or static/dynamic link library).

[interpreter](http://craftinginterpreters.com/a-map-of-the-territory.html#compilers-and-interpreters): a program that takes some input (usually source code) and executes it. An example would be the Python interpreter.

[transpiler](http://craftinginterpreters.com/a-map-of-the-territory.html#transpilers): A program that takes source code from one language and output source code in another language. Actually it could also not be strictly programming language. [Pandoc](https://pandoc.org/) for example let you transpile from various text format to another like from .docx to .md. For programming language, I saw a lot of transpilation going on around Javascript like ClojureScript, TypeScript... Transpilers are sometimes called *source-to-source compiler*.
