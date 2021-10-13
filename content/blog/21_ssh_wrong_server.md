---
date: "Sat, 22 May 2021 19:00:00 +0000"
title: Wrong domain to test SSH setup (Gitlab)
url: '/blog/wrong-domain-to-test-ssh-setup'
draft: false
tags: [ssh, gitlab]
featured: false
toc: false
summary: 'Little story on how not reading properly instructions can lead to lot of time wasted'
---

# Wrong server address for SSH

## Story time

I was setting up SSH to be able to contact Gitlab repositories.

But at the very end, when comes the step to test if the connection is
succesful, I followed the [Gitlab
documentation](https://docs.gitlab.com/ee/ssh/#verify-that-you-can-connect)
(use the command `ssh -T git@gitlab.example.com`) and it was
not working.

I had no trouble setting it up for Github a few days before, and the
setup is pretty similar. I could not pinpoint what was going on at
first.

I figured I must have gone a bit fast following the docs, recreated a
new pair of keys, tried again, and still no luck.

I went to the [Github
documentation](https://docs.github.com/en/enterprise-server@2.22/github/authenticating-to-github/testing-your-ssh-connection)
to set up SSH, started from scratch again. And still, when it was time
to test the connection, it would not be working.

And then, after at least 30min of trying to understand why, the
realisation came to me (and maybe you understand as soon as you saw the
command): the domain in the doc is not a real one, so I was trying again
and again to test my SSH connection again a non-existent domain.

So if I had skipped that step and went straight to cloning a repository,
it would have worked!

## Conclusion: read carefully

Lesson for this time: even when following a step-by-step guide for
something that should be trivial, pay attention to what the command
contains to not waste time debugging afterward.
