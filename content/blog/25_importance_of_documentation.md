---
title: 'Some thoughts about documentation'
url: '/blog/some-thoughts-about-documentation'
date: Tue, 21 Dec 2021 19:00:00 +0000
draft: false
tags: [documentation, short]
# image: "../img/me.jpg" # this is how to add images to the post in the /blog section. Might be useful later, even though right now it's a bit ugly if the image is not small.
featured: false
toc: false
summary: 'Some thoughts about documentation and how good it is after a few years of working.'
---

Writing down a few thoughts about documentation, as I realized a few things in the past two years that I did not write down yet, so here we go.

## Knowing the audience

As software developers, we write documentation but we don't often reflect on who is targeted by the documentation. Sometimes, it's the end user that will read it, sometimes it's a colleague from another team, sometimes it's a non-technical person, sometimes it's us in the future.

Depending on who is the most likely reader, we should adapt how we write. A user and/or non-technical person does not need internal or technical details about the system, but might need a glossary, a tutorial and non-technical vocabulary.

On the other end, for technical colleagues or for ourselves, we can probably skip the glossary, and we probably wants to dig deeper in details, because that is usually what matters most.

## Knowing the needs of the audience

Related but not quite the same, the same user can come to your documentation multiple time with multiple needs. One time he needs a "getting started" section to get up to speed quickly, the time after he might want to find some "tutorial" to get going, and the next time he would look out for "references" documents to help dig further.

Same could apply for internal documentation, writing a tutorial vs a getting-started guide is not the same and should be treated differently.

## Give context

This is more for the technical documentation, either architectural decision record, design document, or most technical document you can think of. This is also applicable for commit message (if using Git).

Giving context about the system or a change is probably one of the most important things to do.

If you give enough context (requirements, constraints, how the system behave at time of writing), other people should be able to understand why the solution made sense at that time. It is also a good idea to talk about alternative solution, why they were not chosen (a little list of pros/cons can help).

## Diagram are important

A picture is worth a thousand words. Use diagrams/schemas.

And if you do use diagram, please include the source of the diagram to make sure anybody can update it later.


## Write more documentation

We usually are reluctant to write documentation, but it is very rare in my experience that we write enough of it.

Something that can help you write documentation is [Scott Hanselman's take on keystrokes](https://www.hanselman.com/blog/do-they-deserve-the-gift-of-your-keystrokes).
Instead of responding on your messaging app or in a mail to one person asking a question, write a blog post or in a wiki or in whatever format make sense, then share it. Overtime, you should be able to link people to your posts instead of responding over and over to the same questions.

## Can there be too much documentation?

Yes. Documentation has a maintenance cost. Outdated documentation can even be dangerous because it can lead to false impression on a system.

But with a little discipline, and dating the documentation you write to easily spot text that was written a few years ago versus text written last week, that should not be a problem.

And frankly, it's rare enough to have too much documentation anyway so this should not be a problem in most cases.

## More material

Some links to learn about documentation in various forms:
 
- [What nobody tells you about documentation - Daniele Procida - PyCon AU](https://www.youtube.com/watch?v=t4vKPhjcMZg). Wonderful presentation, from the experience the Django team has with having great documentation for their project.
- [Django documentation](https://docs.djangoproject.com/en/4.0).  Goes well with the presentation above, can give a good idea of what user-facing documentation can look like when done well.
- [Sébastien Portebois's tips for better architecture diagrams](https://notes.portebois.net/2021/03/23.html). One of the architect on the project I work for put up together a really thorough post about diagram. Cherry on top is the checklist at the end to evaluate your diagram quickly against common mistakes.
- [How to write a great commit message](https://cbea.ms/git-commit/) A classic, to be on top of your git-commit game.