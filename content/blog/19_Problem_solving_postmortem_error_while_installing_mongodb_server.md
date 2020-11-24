---
title: '"Systemctl not found" while installing MongoDB server'
url: '/blog/systemctl_not_found_while_installing_mongodb_server'
date: Sun, 22 Nov 2020 19:00:00 +0000
draft: false
tags: [nanowrimo2020, post-mortem]
featured: false
toc: true
summary: 'Post-mortem of a problem-solving session that occurred to me recently.'
---

# Post-mortem

_This article was drafted during [NaNoWriMo 2020]({{< relref "17_My_NaNoWriMo_in_2020.md" >}})._

I hit a frustrating problem while deploying to production at work so here is a little postmortem.

## The problem

The problem came from a Jenkins build, from a repository that had not been updated since a week. We wanted to deploy from a _release_ branch to _master_. The error was from an installation of MongoDB server 4.2 in a Dockerfile (not an official Dockerfile from MongoDB but an install of MongoDB server in our own Dockerfile).

## Spoiler: a solution

I'll give right away a solution for people that don't want to read all of it.

The problem was that MongoDB server 4.3 and 4.4 had a change in a post-installation script (only for Debian I think) that adds a call to `systemctl`. That change was backported to MongoDB server 4.2 in October, and was part of an update to the `deb` package `mongodb-server` around November 15.

If `systemctl` is not present (in my case, in the Docker container based of a Debian image) when the post-installation script is called, the installation of the package fails and the build breaks.

To circumvent that problem, you can run `ln -s /bin/true /usr/local/bin/systemctl` (or `RUN -s /bin/true /usr/local/bin/systemctl` in the Dockerfile) before trying to install `mongodb-server`. This will skip the `systemctl` call.

_You should probably not do that if you need `systemctl` elsewhere in your system though._

## Starting the investigation

At first I thought our deployment pipeline had a hiccup. The build was fine on the _develop_ branch and on the _release_ branch. It sat there for a full week with no problem. I also checked the last commit from the week before and it was a change to the frequency of a cron job. Not really a **build-breaking** change usually.

I launched the build again. Our infrastructure is a bit weak, we have failing build every other day that can be successful by launching them again. This time it did not solve the problem. I also relaunched the build in _develop_ to see if it was a problem with the environment, but it also failed.

I could not pinpoint the problem yet but it seemed as if it were not from the new code, nor from the pipeline. Also, the Dockerfile used an official node image, with an exact version (like `node:X.X.X-slim`) so I expected things to be pretty stable between each run. The `node` image is itself based on `debian-stretch`.

<div style="width:100%;height:0;padding-bottom:56%;position:relative;"><iframe src="https://giphy.com/embed/l0MYM98IwMYDIn1fO" width="100%" height="100%" style="position:absolute" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div><p><a href="https://giphy.com/gifs/sherlock-bbc-one-l0MYM98IwMYDIn1fO"></a></p>

I summoned my inner _Sherlock Holmes_ and went investigating further.

## Looking more thoroughly at the error message

First tries did not work. Now I had to dig into the error message a little more.

```
/var/lib/dpkg/info/mongodb-org-server.postinst: 43: /var/lib/dpkg/info/mongodb-org-server.postinst: systemctl: not found
dpkg: error processing package mongodb-org-server (--configure):
 subprocess installed post-installation script returned error exit status 127
dpkg: dependency problems prevent configuration of mongodb-org:
 mongodb-org depends on mongodb-org-server; however:
  Package mongodb-org-server is not configured yet.
```

The error message above contains some information:

- Line 1: `/var/lib/dpkg/info/mongodb-org-server.postinst: 43: /var/lib/dpkg/info/mongodb-org-server.postinst: systemctl: not found` means that at the line 43 of a file `mongodb-org-server.postinst` there was a call to systemctl that failed because the program was not found
- Line 2: `dpkg: error processing package mongodb-org-server (--configure):` this one tell us that its the mongodb-org-server package called with the `--configure` package that went badly
- Line 3: `subprocess installed post-installation script returned error exit status 127` a bit more detail for the line above, not too much info here. _This looks like a weirdly worded message_
- Line 4-5-6: Other information that comes from the first messages above.

If you put the first line of the error message `/var/lib/dpkg/info/mongodb-org-server.postinst: 43: /var/lib/dpkg/info/mongodb-org-server.postinst: systemctl: not found` into a search engine, you find a SO post[^1] that contained only an answer at the time and a post with a very similar error. His answer was cryptic to me, howewer, so I had to dig again.

{{< figure src="/img/blog_19_unhelpful_SO_answer.png" alt="Answer that says 'echo ln to /bin/systemctl'" >}}

In the SO post, there is a link to the official Dockerfile for MongoDB 4.4 so I went to take a look.

## The truth is in the Dockerfile

After reading it a bit and searching for `systemctl`, I found this line in the Dockerfile[^2]:

```yaml
# starting with MongoDB 4.3, the postinst for server includes "systemctl daemon-reload" (and we don't have "systemctl")
&& ln -s /bin/true /usr/local/bin/systemctl \
```

So that was a good hint. Thanks to all the developers that comments their code :) I tried to add `RUN ln -s /bin/true /usr/local/bin/systemctl` to my Dockerfile and it worked! The build was fine now.

But it's not over. I had to understand it to see if it would come bite our project later or if this adds some constraints on the repository because we skipped a step in a post-install script.

## Understanding why that fixed the problem

### The post-install script and systemctl

The part that failed is here[^3]:

```sh
# Check for changes to the service file
systemctl daemon-reload
```

`systemctl` is a part of a group of software called `systemd`[^4].
`systemd` _provides a system and service manager that runs as PID 1 and starts the rest of the system_ (from their website).

### What is `/bin/true`?

I discovered `/bin/true` as a command (`/bin/false` exists too!). It's a command that you can use to always return 0 (which is a truthy value in the shell). This is like using a constant for true in shell scripts.

### What is `ln`?

I knew about `ln` for links between files on the system before but I never saw it used to replace the comportment of a command by another one.

```sh
ln -s /bin/true /usr/local/bin/systemctl
```

This command creates a _symbolic link[^5]_ (not a _hard link[^6]_) from `systemctl` to `/bin/true`. Like a pointer to it: when you call `systemctl`, it will redirect you to the `true` command, and execute it instead.

### Putting it all together

So we have seen that using `ln` in combination with `/bin/true` and `/usr/local/bin/systemctl` did work to allow the build to succeed. But is that right to make it build with a step that is skipped?

In fact, I don't know this one for sure (if you know tell me please). My understanding is that the post-install script uses this command to update the configuration of a service _if it exists_. But in my case I don't have it so it should not matter if I don't update it.

## Understanding why it hits us today and not before?

Another thing I would like to understand is why this happened to us this Thursday and not a week ago on the last build, or even a few months before? Maybe answering that could help prevent other build failure in the future.

As it turns out, looking around in the repository for the Dockerfile for MongoDB where I found the trick to solve my problem, there is a comment[^7]:

```sh
# starting with MongoDB 4.3 (and backported to 4.0 and 4.2??),
# the postinst for server includes an unconditional "systemctl daemon-reload"
# (and we don't have anything for "systemctl" to talk to leading to
#  dbus errors and failed package installs)
```

It indicates that the change for 4.3+ might have been backported, let's find out.

The following commit[^8]

{{< figure src="/img/blog_19_call_to_systemctl_big.png" alt="Git history for the post-install script, version 4.2" >}}

is for the change in February and it has been backported to 4.2[^9] on October 15.

{{< figure src="/img/blog_19_v4.2_history_big.png" alt="Git history for the post-install script, version 4.2" >}}

(and has been backported to 4.0[^10] then reverted on November 11)

{{< figure src="/img/blog_19_v4.0_history_big.png" alt="Git history for the post-install script, version 4.0" >}}

It's getting closer but not quite, we last built our project successfully November 12, and it failed November 19. So what could have caused this?

### Finding when the Debian package was updated

My best guess right now is that the Debian package `mongodb-server` we use in the Dockerfile was updated between the two dates.

I ended up looking at [a website for Debian package](https://packages.debian.org/stretch/database/mongodb-server), trying to get the changelog but I hit a 404 on the changelog page. Not helpful.

I also found the [dpkg list](https://debian.pkgs.org/9/debian-main-amd64/mongodb-server_3.2.11-2+deb9u1_amd64.deb.html) for `mongodb-server` but I dis not find a date of update in there.

Right now, I don't know how to get the information apart from contacting a maintainer directly. I'll edit the post when/if I find the response.

## A couple other considerations

### Putting it in the SO post

I tried to [recap what I learned in the original SO post](https://stackoverflow.com/a/64949118/3729797) I found at first to help others in the same boat.

{{< figure src="/img/blog_19_my_SO_answer.png" alt="My take on answering the SO post" >}}

Amusingly, the day just before I wrote my answer, someone answered as well in this question. It would have saved time to me but I would not have looked into the problem as much but I am glad I did, I discovered a lot of stuff.

### Not deploying into master at 4.30p.m.

This one should be common sense but we hit this problem around 4.30p.m. on a Thursday and that was not fun. It did not break anything but it add a lot of stress to break a build at the time when you just want to end your day.

I'd like to thank my colleague Maxime who was with me at that time and did some initial investigation on the Jenkins builds with me.

### Maybe another solution?

While I looked around, I tried to search with `systemctl not found` into a search engine and found another SO post[^11] that proposed to install `systemd`. I did not try it but I expect it might have worked.

But it is not the relevant solution for me. We would not use `systemd` or `systemctl` in this repository so I feel it is better to skip this part of the script we don't need, instead of installing a "dead" package.

## Conclusion

All in all, this problem took only a couple of hours to get fixed but it was annoying because of the circumstances. Having a build fail because of an external package being updated is never fun.

But it was a good learning experience, I discovered `/bin/true` and `systemctl`, I got to look at the MongoDB open-source repository and the docker-library repository and the impact stayed low on the project so not too bad. 😀

[^1]: [The initial SO post](https://stackoverflow.com/q/63709096/3729797)
[^2]: [Official DockerFile for MongoDB server](https://github.com/docker-library/mongo/blob/master/4.4/Dockerfile#L91)
[^3]: [mongodb-org-server.postinst l43](https://github.com/mongodb/mongo/blob/0690089e24d1ae398b3cafc348a0b9c9f5f24527/debian/mongodb-org-server.postinst#L43)
[^4]: [`systemd`](https://systemd.io/)
[^5]: https://en.wikipedia.org/wiki/Symbolic_link
[^6]: https://en.wikipedia.org/wiki/Hard_link
[^7]: https://github.com/docker-library/mongo/blob/8afebb1ef44b8076f9f4472c689409835707399d/update.sh#L163
[^8]: https://github.com/mongodb/mongo/commit/0690089e24d1ae398b3cafc348a0b9c9f5f24527#diff-2c47e73d74099557344473c7c23c11843015cfd12316d1d19ba5b008dfa6ece2
[^9]: https://github.com/mongodb/mongo/blob/v4.2/debian/mongodb-org-server.postinst
[^10]: https://github.com/mongodb/mongo/blob/v4.0/debian/mongodb-org-server.postinst
[^11]: https://askubuntu.com/q/988266
