---
title: 'Scaling Code Review'
url: '/blog/scaling_code_review'
date: Sat, 10 Oct 2020 19:00:00 +0000
draft: false
tags: [code-review, improving]
featured: false
toc: true
summary: 'Trying to improve the code review process when number of developer increase.'
---

# Scaling Code Review

Recently I hit a problem with my team. Our code review process did not scale up with our team growing (especially in the last weeks). Here are some reflexions on that problem and some reflexions on what we choose to do to see if it improves our process.

## Context

We started as 3 developers more than one year ago, and grew to 6 developers now (and the occasional intern that I will not take into account in my rambling, even though it adds a bit of noise on the line). Our professional experiences range from 3 years to 20+ years as developer.

## Our Code review Process

The process went a bit like this:

- you finish a task
- you commit/push your final code on a feature branch
- you open a pull request
- you assign the whole team as reviewer
- you wait for someone to notice (or you go bug people around on the next daily scrum)
- the rest of the team look at it (eventually), add comments if they feel necessary
- you address the comments either by making a change in the code or by responding to the comments (can include a live discussion if there is a lot of clarification to do, or a strong disagreement)
- repeat the last two steps until we can come to an agreement
- anybody merges the code if there is an approval (or decline the PR if appropriate)

For small task it was mostly OK, and on bigger task we would have a lot of back and forth but pretty often we would end up on a meeting just to chat and agree on what needed to be done or not. Not too time consuming.

_Side note: we also have a document/checklist with what should be done for a PR to be merged, like presence of unit tests, does it pass all criteria for the task, respect rules in [sonarqube](https://www.sonarqube.org/) etc. I really like that we have this explicit checklist, even if it is not enforced as hard as we probably should._

Until four people it stayed OK, but the fifth started to push the limit of this process.

What happened most of the time is that at the start of our two-week sprint, everybody went on to take tasks, code happily, and pull requests are opened. Then people started to look at other people's pull request a bit, comment on some things, then go start a new task and sometimes start to address some of the comments on their own pull request. Pull requests took a long time to be closed, and there was always more PR being opened than PR being closed. Then at some point, the pile of stuff to do was empty so people started to look again at the PR, but by that time there was a lot of them, and keeping track of everything was hard.

Code reviews became frustrating and also a bottleneck because QA was waiting for the new features to come out to test them but they had the first week of the sprint almost empty, and the second week very busy. This is far from ideal.

To try to fix it, we added a new rule that as a team we MUST close PR as fast as possible. It's a vague requirement but the intent was for people to try to close their PR before taking some more work. It helped but it was not enough, and the sixth developer came shortly after we introduced this rule, and it became unbearable again.

Now I'm looking to scale up our process, with some suggestion and input from the team.

## Problems that we won't solve this time

### Diffents views on what is clean code / code that can go to production

One aspect that takes a lot of time is discussing what is acceptable or not.

Some of us are really strict in how much unit testing must be done, how good naming is important, how conventions should be followed to form a coherent code base (not only convention enforced by linter).

And some of us are more "result focused" or "output focused" as they like to say. If it "works", even if it's a horrible hack, you can ship it.

_I lean strongly on the first camp, can you tell? My biggest pet peeve is naming. But I also recognize that sometime shipping working code sooner is more important than polishing it for 3 months. As always, you must find a good balance and context can have a huge influence on how much time you have to refactor/clean your code_

I think this problem is somewhat tangential to our time problem. We need to align people better on what production code should look like, but that is not our main problem in this case I believe (_but I could be terribly terribly wrong._)

I watched a few of [Kevlin Henney's talk](https://www.youtube.com/results?search_query=kevlin+henney) recently and I think he has some good bit on this aspect of programming (and many other, go take a look).

And of the course the obligatory xkcd comic:

![Code Quality](https://imgs.xkcd.com/comics/code_quality.png)

## Metrics

First step: to solve this problem I want to gather data, to be able to tell in the future if the changes we implemented as a team works or not. (_But be cautious that when you introduce metrics, people find way to abuse them._)

### Measuring time to close PR

One metric I am interested to improve is time elapsed between opening the PR and closing it. (To be more accurate, one would need to count only business day, that's what I intend to do.)

If a PR is opened and closed on the same day, I will count the time as 0 day. I'm not interested to be more precise than a day here but one could also count it in hour. I don't think it will benefit us though as our PR stay open almost always more than a day I guesstimate _spoiler alert: I was mostly wrong on that guess_

### Measuring day of the sprint the PR are closed

Another thing I am interested is on which day PR are closed. If we close all our PR on the last two days of the sprint, it leaves very little room for QA to do her job properly. It's also a very frustrating experience to have fixes incoming, just before the demo or even live during a demo. It's nerve wrecking, and not how I envision to work as professionally as possible.

### Number of comments on each PR

This one is mostly curiosity on my part: does amount of comment correlate closely with time to close PR?

Intuitively I think that it would be the case but sometimes you get surprise with intuition so I wanted to know. _spoiler alert: it does correlate weakly, but less than what I thought_

### Data in image

I took advantage of 35 min before the last meeting of the week, on a Friday morning to gather the data on our sprint. I did it "by hand", opening every PR that was closed since the start of the current sprint and filling a table in Excel. I think it took me around 20 min to get all the data. If I had to do that every week, I would like to automate this process. Maybe Bitbucket has an API or a plugin? But for now I intend to do it at most three times so I do not see the need to automate it.

Here are three graphics I plotted in Excel with the data I freshly gathered (_with some French in it_):

The first one is the number of days to close a PR on the x-axis, and number of PR that were closed in that many days in the y-axis.

{{< figure src="/img/blog_15_pr_data_correlation_comments_vs_time_to_close_a_PR.png" alt="Number of days to close a PR">}}

This shows that contrary to my guesstimate above, we do close quite a lot of PR in less than one day. Obviously those includes configuration changes, very small fixes and people merging their own PR without waiting code review to go fast... But still, it's a decent number.

On the opposite spectrum, we have some PR that took more than 2 days to close. (The one that took 11+ days was a draft PR opened at the start of the previous sprint, and closed at the beginning of the current sprint. It is an outlier but I kept it anyway). Those are the one we are looking to get rid of.

The second plot is the frequency of PR merged by day. x-axis is the day of the week (in French but you can probably work with it ;) ), with the week number as a suffix. "1" for the first week of the sprint, "2" for the second week.

{{< figure src="/img/blog_15_pr_data_frequency_of_PR_merged_by_day_of_sprint.png" alt="Frequency of PR merged by day of the sprint">}}

This one is not too surprising I think, lots of PR being closed on the last 3 days before the demo on the last Friday of the sprint.

And the last one, the number of comments on the y-axis and the time it took to close a PR in day on the x-axis.

{{< figure src="/img/blog_15_pr_data_correlation_comments_vs_time_to_close_a_PR.png" alt="Correlation number of comments vs time to close a PR">}}

This did surprise me a bit more. You can still see that every PR that stayed open more than 2 days have some comment in it.

But we have also PR with 30 comments that were closed in one day.

I don't know how to interpret this one yet, I will wait for the next time to see if I can get more insight. Maybe I'm also needing more context on each PR to better understand what this graph shows?

## Ideas to improve the process

Here I will present some of the ideas we brainstormed, the one we decided to try to implement and the one we did not pick.

### Instead of putting everybody as a reviewer on each PR, we could only put two peoples.

Benefits would be:

- to reduce noise (less cooperation needed, less time spent finding a time where everybody is available)
- allows to have a tie when disagreeing (two reviewers and the one opening the PR)
- faster agreement
- time spent overall for the team on one pull request should be divided by a factor X, with X probably in the range [1.5, 3] if I'm being optimistic. There is nothing scientific in that estimation, only gut feeling

Cons are:

- people not reviewing the code will be less familiar with the whole code base (unless they go read the PR for themselves but restrain from commenting unless big mistake).
- how to choose the pair to review
  I'd like to try this one, but I'm a bit torn on how to choose the pair of reviewers. It is always the same people together? Random pair? Familiarity? Time available at the time of deciding who's going to take care of it?

### Put one person in charge of all PR

One developer main task for a sprint could be to review and approve/fix - we decided against this. The goal is still to share knowledge for the whole team.

### Put one person in charge of one PR

Each PR has one main reviewer - We are going to try this.

We put everybody as a reviewer on the PR in Bitbucket, but only one of use is responsible for accepting/declining. Everybody is still welcome (and encouraged) to go take a look at the PR, and comment on things that are major flaws.

### Put one person in charge of MERGING all PR related to one repository

This one was - in my eyes - an interesting suggestion.

Sometimes the main bottleneck was that 3 or 4 PR landed on the same repository, for many different features and write in the same files. Without someone in charge for the coordination, it looks like a mess to merge everything.

We decided to go with this one, and to keep things light, to just decide at the daily scrum in the morning if we needed to have people step up for the merge-coordination duty.

### Don't commit uninvited to someone else's branch

This one should be a no-brainer but if you push things on somebody else's branch without communicating/cooperating, bad things might happen. At the very least, it can be frustrating.

While obvious, I (and others on the team) have been doing it on some occasion so we are trying to reiterate on the fact that we should really avoid doing this.

### Flag comment in PR with priority

Some people were complaining that sometimes we would not close PR because there were comments that nobody answered, but those comments were irrelevant, only rhetorical or not important.

So we decided to add a little flag like "question:", "important:" in our comment when applicable to help the reviewer get a feel for when everything important is fixed.

This one I have mixed-feeling about, I'm sure most of the team will forget about it in the first 2 days, me included. It's a nice-to-have, but I have a feeling it will take a long time before everybody follow this.

## Conclusion

So we are trying right now all of this, but we are also doing the biggest release in production for the project since 18 months so I expect that we won't follow half of what we set out to do.

Still now we have some really nice ideas to improve, and when things settle down I expect the team to abide by the new rules we set for ourselves.

As an aside, the team did a really good job to come up with ideas on how to improve, and the process was very smooth. All the frustration from the previous weeks on this topic turned into a very productive 30 min meeting on getting better at it.

It does not always end up so well, but I think it is also the result of our team growing together. Whatever the outcome of our experiment is, we are still a better team this week that the previous one and that is amazing to witness.
