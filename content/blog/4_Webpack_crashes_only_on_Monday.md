---
title: 'Webpack-cli crashes only on Monday'
url: '/blog/webpack-cli-crashes-only-on-monday'
date: Sat, 06 Jul 2019 20:00:00 +0000
draft: false
tags: [short, bug, webpack, webpack-cli, not-the-onion, javascript]
featured: false
toc: false
summary: "How subtle bugs can be sometimes..."
---

# Webpack-cli crashes for Windows users, but only on Monday

It seems like a headline from the Onion[^1]. But unfortunately it's not.

Webpack-cli[^2] had a bug[^3] recently because it tried to display a message for donations on the terminal when using it. But that message was only displayed on Monday(because there were complaints about seeing the donation message every time you were running a build).

Then a bug was introduced into that code because it relies on `process.getuid ` which is not available on Windows, making the CLI crash but only on Windows and on Monday \\(**)/.

## Similar story

If you like weird bug story, you can also read:

- [Crashes only on Wednesday](https://gyrovague.com/2015/07/29/crashes-only-on-wednesdays/)
- [Cannot print on Tuesday!](http://catless.ncl.ac.uk/Risks/25.77.html#subj14)
- [The 500-mile mail](https://www.ibiblio.org/harris/500milemail.html)

[^1]: https://www.theonion.com/
[^2]: https://github.com/webpack/webpack-cli
[^3]: https://github.com/webpack/webpack-cli/issues/962

