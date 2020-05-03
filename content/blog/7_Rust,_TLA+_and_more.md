---
title: 'Rust, TLA+, Aesthetics and more'
url: '/blog/rust_tla+_and_more'
date: Sun, 05 Apr 2020 19:00:00 +0000
draft: false
tags: [rust,tla+,book,aesthetics]
featured: true
toc: true
summary: "Let's talk a bit about Rust, TLA+, Aesthetics in programming and a few more things."

---

# Rust, TLA+, Aesthetics and more

It's been a while I have not  put my thoughts in here, so this entry will be a mix of some of the papers or blogs I read recently, some thoughts about Rust and TLA+ because I started trying them both recently, and a few interesting talks I saw recently. The Aesthetics part will be discussed in the blog entry that I read recently and was an interesting reflection on how tools shape our way of thinking.

## TLA+

I'll start by talking about TLA+ because it lead me to other subjects I'll talk after. I discovered TLA+ after reading a few blog posts seen on [HackerNews](https://hn.algolia.com/?q=tla%2B).

Basically the idea behind TLA+ is that writing software that is correct is extremely hard to do (more so is you include concurrency/parallelism in your software). TLA+ is a high-level language for modeling systems, that relies on math to check the correctness of the model. It has been used successfully by engineers in big company like Amazon and Intel to help them design robust systems.

TLA+ is mainly the work of [Leslie Lamport](https://en.wikipedia.org/wiki/Leslie_Lamport) (I did not know about him before), a fairly well-known computer scientist known for his work in distributed systems (and being the initial developer of [LaTeX](https://www.latex-project.org/)). He received many prizes including the Turing award in 2013.

On [The TLA+ Home Page](http://lamport.azurewebsites.net/tla/tla.html) there is lots of resources to start using it, links to the toolbox, an introductory video course and more information to get started. I am currently at the seventh lecture of the introductory course and it is an interesting concept that I'm willing to try to put in practice. I'll probably come back writing back more on this.

## Rust 

[Rust](https://www.rust-lang.org/) is the "new" language (since 2010) developed by Mozilla with the goal to have a "safe", low-level and performant programming language (as an alternative for C/C++). It features quite a few interesting ideas that I find intriguing and I decided to try it out. 

What drew me in was: 

- immutability: something that I really like about Clojure as well! In Rust it seems it's an opt-out feature, if you declare a variable like `let x = 0` it is immutable but you can opt-out with the keyword  `mut`  (as in   `let mut x = 0`)
- low-level: I have a very limited exposure to low-level languages (and concepts). I did quite a lot of C in my university classes and an internship in C++ dealing with OpenGL but to be honest I forgot most of it. So that will be a good opportunity to get back to low-level programming.
- no runtime: I read about that and I have no idea what does this mean in practice but I want to find out! Right now my idea of that is that Rust is embeddable is other programs via C/C++ FFI with no overhead. I hope to be able to call Clojure code from Rust and vice versa (I saw some repos and blog post about it so it seems definitely doable).
- safe: It seems Rust does a lot of compile time checking to ensure safety and correctness (even for threaded/concurrent program). As far as I understand, it is thanks to is rich type system.

Another good point is that because Rust is a recent language (and good fundings), it has all the niceties like package manager (Cargo), utilities for documentation included (seriously running `cargo doc --open` is really a nice experience!), nice integrations to the majors editors, installation is very straightforward, so far documentation is really high quality...

I started the [Rust book](https://doc.rust-lang.org/book/), very nicely done so far (I'm in the middle of chapter 3).

I will be progressing toward the end of this book and then try my hands with it. It should match very well with TLA+ as well.

## Blogs

I won't talk here about all the interesting blog posts I read since the last time I wrote in this blog (a few months ago) but I wanted to highlight and share some very interesting read I had this past few months:

### [Software, Aesthetics, and Craft: How Java, Lisp, and Agile Shape and Reflect Their Culture](https://www.infoq.com/articles/software-aesthetics-craft/) 

In this post, the author compare the difference between "classical" and "gothic" architecture (real architecture, not software architecture), the elements that characterize both (notably in how much freedom is given to the craftsman) and how we can think about programming language in the same way.

This piece is really nice and I really think you should read it too :) 

### [How I became a better programmer](https://jlongster.com/How-I-Became-Better-Programmer) from James Long (creator of Prettier)

This post is like many others on the internet where the author describes how he try to be a better programmer. Very quickly his point his:

- Find inspiration in other people put keep a critical eye on their work
- Don't feel pressured to work all the time, truly revolutionary stuff happens only every few year at best
- Ignore fluff. Learning syntax and the specific API of a new tool is far less interesting in the long run compared to deep knowledge like algorithm, data structure, how a compiler works, etc
- Look for previous research about what you are trying to solve, it will probably change your mind in a lot of ways
- Take on big projects. Get uncomfortable. By trying to accomplish new thing you learn much more

And then he list a few things that helped him be a better programmer:

- Learn C
- Write a compiler
- Learn macros (as in Lisp macro, not C macro)
- SICP
- Understand continuations
- Try a new language

[His blog](https://jlongster.com/#posts) his overall pretty interesting too so go take a look :)

### [Viability of unpopular programming languages](https://www.johndcook.com/blog/2018/04/17/unpopular-languages/) by John D. Cook

The blog posts of John are very interesting (and very short) so if you like a quick read go for it!

In this one he rants about how we rank programming language by popularity (whatever that means) but that does not define how viable that language is (taking Perl, Common Lisp, F# and Erlang as examples).

### Misc

- [Debugging designs with tla+](http://muratbuffalo.blogspot.com/2018/10/debugging-designs-with-tla.html?m=1)
- [Programming Models for Distributed Computation](https://github.com/heathermiller/dist-prog-book) Not a blog but a repo containing a book about distributed computation
- [Google Technical Writing Courses](https://developers.google.com/tech-writing)
- [Zero-cost abstractions in Rust](https://carette.xyz/posts/zero_cost_abstraction/#rustprogramminglanguage) 
- [Modern Common Lisp](https://ambrevar.xyz/modern-common-lisp/index.html) 
- [Daniel Miessler Tutorials](https://danielmiessler.com/study/) I read it mostly for the infoSec part

## Videos

Here are a few conferences talks about Rust

[Considering Rust (Jon Gjengset)](https://www.youtube.com/watch?v=DnT-LUQgc7s) Making the case for Rust, while also putting emphasis on the weaknesses of it. 

[Rust at speed - building a fast concurrent database (Jon Gjengset)](https://www.youtube.com/watch?v=s19G6n0UjsM) Discussion about the experience of writing Noria, a high-performance database prototype in Rust

[How Rust view tradeoffs(Stephen Klabnik)](https://www.youtube.com/watch?v=2ajos-0OWts) Great view about the Rust culture and decision making



## Books

While reading about TLA+ and Leslie Lamport, I stumbled upon the [ACM Digital Library](https://dl.acm.org/acmbooks) which has free books on programming! Notably one of the latest book is a compilation of [the work of Leslie Lamport on concurrency](https://dl.acm.org/doi/book/10.1145/3335772).