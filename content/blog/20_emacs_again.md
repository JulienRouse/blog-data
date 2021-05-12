---
date: Sat, 08 May 2021 19:00:00 +0000
title: Emacs again
url: '/blog/emacs-again'
draft: true
tags: [emacs]
featured: true
toc: false
summary: 'I give Emacs another try!'
---

# Emacs

Since the start of January, I am trying to switch back to
[Emacs](https://www.gnu.org/software/emacs/)!

Here are some modes (packages/plugins in Emacs lingo) that I want to
try.

## org-mode

I am really drawn out to [Org Mode](https://orgmode.org/), a major mode
(a mode is a plugin in Emacs) to help with todo-lists, planning,
agendas, literate programming and a lot more!

I used it in the past with
various success, but because I could not use Emacs at work. I stopped using Emacs altogether
to stop switching between editor. I had
settled for the [Foam plugin](https://foambubble.github.io/foam/) for
VSCode, which offers some nices todo-list capabilities, an also a good
base for growing a knowledge base in the
[Zettelkasten](https://en.wikipedia.org/wiki/Zettelkasten) way.

Since then, an extension for org-mode appeared:
[org-roam](https://www.orgroam.com/), wich looks like a serious
contender I would like to try.

(And actually this post is written with org-mode, and exported to
Markdown with [ox-pandoc](https://github.com/kawabata/ox-pandoc), an
extension to use the magnificent [pandoc](https://pandoc.org/) to export
org files to various format.)

## Magit

This one is totally new to me, a
[porcelain](https://stackoverflow.com/a/6976506/3729797) for Git really
well integrated in Emacs.

I have heard a lot of good things on [Magit](https://magit.vc/), so it
is on my list of packages to try!

## lsp-mode

[lsp-mode](https://emacs-lsp.github.io/lsp-mode/) add support for
[language server
protocol](https://microsoft.github.io/language-server-protocol/) in
Emacs. I think this idea came originally from Microsoft/VSCode to
simplify the development of tooling for new language and editors. This
looks nice and should allow to have a good experience for most
programming language.

Additionally, there is some integration with popular packages like Helm,
Ivy and other as well so it might help me to discover new packages.

[eglot](https://github.com/joaotavora/eglot) also add support for lsp in
Emacs, but looks less complete at first glance.

## Other packages that look promising

And some less well known packages:

### docker.el

[docker.el](https://github.com/Silex/docker.el) is unsurprinsigly a mode
to work with Docker container in Emacs.

### kubel

[kubel](https://github.com/abrochard/kubel)
is a package for managing Kubernetes cluster.

I discovered it while watching this very very good video: [Conquering
Kubernetes with Emacs](https://www.youtube.com/watch?v=w3krYEeqnyk)

## Conclusion

I am back in Emacs land, so much to (re)discover.

And if you never heard of org-mode before (or
[Emacs](https://www.gnu.org/software/emacs/)), make yourself a favor and
go try it for yourself. It is such a different experience that you will
learn something for sure.
