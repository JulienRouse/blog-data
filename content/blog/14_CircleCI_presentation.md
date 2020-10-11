---
title: 'CircleCI presentation'
url: '/blog/circleci'
date: Tue, 16 Jun 2020 19:00:00 +0400
draft: true
tags: [circleci,continuous-integration]
featured: false
toc: true
summary: "circleci"
---

# CircleCI

I attended a presentation given by Chris Black today about CircleCI. I know about the product and company since a few years, initially I found them because they use Clojure :) CircleCI is a continuous integration tool (the CI in the acronym CI/CD *continuous integration/continuous deployment*). 

Below is some of the notes I took about it, and I'll put a link of the video as soon as they publish it publicly.

## Why CircleCI?

Software development went from custom code, custom deployment 10+years ago to dockerization/containerization that has standardized deployment a lot. Also idea like Continuous Integration, Continuous Delivery, were very new when CircleCI launched but are now a big thing.

Also almost every company now is a software company, willing or not. They have to handle deploying software.

That's were CircleCI comes in, offering a powerful, cloud native and highly customizable continuous integration environment. We will go around a few of the nice features it has to offer.

### Replacing CI in context

Quick note about where CI/CD: code -> VCS(git, mercurial, etc) webhook -> circleci

Then :

circleci -> apps integration (sending a message on a chat when the build fail, a mail when the build needs approval, etc)

circleci -> code coverage

circleci -> security analysis

circleci -> building code 

!!!! find the image that goes with that? !!!!!

## Security

The emphasis on security is because they provide a deterministic build, in a fresh environment (with network isolation)  for each build.

https://circleci.com/docs/2.0/security/

## Workflow

Workflow are pipelines. They define the orchestrations for the jobs (or builds). Workflows are DAGs (Directed Acyclic Graphs) ??benefit??

The workflow are highly customizable: you can define jobs to run in parallel, sequential, and with both you can define if a job depends on another's success, and reciprocally if another job depends on the job's success afterward. Also the ability to filter jobs by branch level, and to restart a workflow from the start or from the point of failure to have a faster build. They have a nice diagram page to explain visually what is possible [here](https://circleci.com/lightbox/workflows/), along with the configuration that allow this kind of workflow.

 The configuration file are YAML files, so no surprises here.

## First class Docker support

One thing Chris put a lot of emphasis on, is the fact that if you have some software that run inside a Docker container, you can use CircleCI to test/build/deploy it.   You can also use pre-build images built and maintained by CircleCI

## Sidenote

Their competency matrix is interesting, look at it some more! https://docs.google.com/spreadsheets/d/131XZCEb8LoXqy79WWrhCX4sBnGhCM1nAIz4feFZJsEo/edit#gid=0

