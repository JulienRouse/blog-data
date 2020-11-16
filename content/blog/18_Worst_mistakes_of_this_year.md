---
title: 'Worst mistakes of this year'
url: '/blog/worst-mistakes-of-this-year'
date: Sat, 05 Dec 2020 19:00:00 +0000
draft: true
tags: [nanowrimo2020, improving, post-mortem]
featured: false
toc: false
summary: 'Reflection on the big mistakes I made this year. What went wrong, what can be done to fix it and how to avoid it in the first place.'
---

# Learning from my mistakes on the job in the past year

_This article is part of the ones I drafted during [NaNoWriMo 2020]({{< relref "17_My_NaNoWriMo_in_2020.md" >}})_

End of the year is coming, and this is also the last two months of the project I was working on for the past 15 months. It is a good time to reflect on the decisions and actions I made and try to see what could have been done better.

I hope you can learn something from my mistakes without to have to make them yourself.

## Not fighting hard enough keep integration tests in the project

### A little bit of context

The project was started three years ago by another team but the production failed so the client asked a new team to take the existing codebase and to deliver something that can go to production. That is where I entered the project.

After a month, I was given a lead role to alleviate the fact that our architect was only dedicated to our project for 10h per week. At that time we were still trying to make sense of everything in the project.

At this time, they were integration test running with Newman, an automated runner for Postman collections. To add tests, you add a postman collection in JSON format to the code repository, and you add in the database the ID of your file to a document that tracks available tests. Then to launch the tests, you call an endpoint in the API.

Our team did not like this way of building tests, having to manually export a JSON out of Postman, then fiddling with a script for the database to add a reference for the new test. But we had to work with the existing codebase so we tried to make it work, waiting for an occasion to make the process smoother.

### The problem

After a few days of trying to work on integration tests, we found out the existing tests were flaky and messy. They did not clean properly the state of the application after running, and some of them would fail randomly. And maybe even worse, a dozen of them would just report that the test succeeded when in fact the test failed but the condition was not checked properly.

We were not particularly happy about that and tried to find a plan to fix it. An argument started about the pros and cons of fixing them versus starting from scratch with another testing framework. It was a bit messy because we had a hard time evaluating how much time we thought we would need to fix it, and starting from scratch was mostly using Selenium and our team was not experienced with Selenium so we were reluctant to embark on this journey.

### The decision

Then we had a big meeting with a project manager and one of main stakeholder in the project. I was invited as technical lead for my team.

The discussion went weird, from my point of view. Basically, the point of the manager was that tests were only useful for developers while working on the project, not to the end user so we should not take time to fix them nor rewrite them. They expected us to abandon all tests for the remainder of the project.

I will not lie, I was speechless for a good 15 seconds. I was not expecting that. I tried to explain that, in fact, the end user would benefit from us having tests in the application because the development will be more robust, we would have more confidence in our codebase when doing changes.

They would not have it, they did not want to "lose time anymore" in the project. So it was settled, I could not find the arguments to keep the tests in the project.
At the end of the meeting I tried to have a deal of reintroducing some automated test a few months down the road so our QA would not do all the tests manually. They reluctantly agreed, telling me it will be prioritized eventually.

Now, more than a year later, we still don't have integration tests. We were not allowed to prioritize tests back into our application. And funny thing, we spend an absurd amount of time in QA session, testing for regression and fixing bugs because we are not confident when we make changes, and we still do not catch all of our mistakes.
It's hard to estimate how much time we have lost this way, or if having integration tests would have saved us time, but I am willing to bet it would have helped us.
And it also took a big moral penalty on the team, by the lack of confidence in refactoring alone.

So lesson learned I guess, you have to pay the price of software quality at some point. And now I would have a lot more arguments if I were to be in the same situation as before to try to convince stakeholders and management to not cut corners on good practices.

But still, I was not able to change the outcome of the meeting because I was not convincing enough. And that is certainly my worst mistake of the past year, with an impact on the whole duration of the project.

## Not monitoring production

_"Failing to plan is planning to fail" Alan Lakein_

Time for a second story: in the last few weeks of october we finally deployed our project to production. There are no user yet but we have a lot of services running that are important.

Just after we put everything in production, we were monitoring it and fixed a few problems.

But then a new week (and a new sprint) started and everyone went to their daily tasks and solving problems for the next release.
And doing so, the whole team stopped looking at the logs and monitoring production.

After a few days, someone got an email from the infrastructure team that their was a problem since two days, our DB memory consumption was very high compared to what is should be. We went to see the logs and dashboard, and we could see it very clearly too.

This ended up causing an interuption in production, which is bad.

So what happened? We did not plan for who would keep an eye on the production environnement, and we were only beginning to put automated alert on monitoring so nothing went off in production. Instead of having two full days to fix production, we had to do everything in half a day.

The consequences this time were not too bad, but it could have been.

From this I think I will remember two things:

- Do not assume somebody will take care of something (even as important as a production environnement) unless it is explicitly told
- Pushing everything in production when monitoring and alerts are not setup properly (or at all) is a huge risk for a project

## Ending word

Hope you enjoyed thoses stories. In the first case the impact has been huge on the project, and on the second case it was close. Both could have been avoided and I hope you will not make the same mistakes.
