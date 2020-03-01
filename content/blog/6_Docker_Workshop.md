---
title: 'Docker Workshop'
url: '/blog/docker-workshop'
date: Sat, 01 Jan 2019 19:00:00 +0000
draft: true
tags: [Docker,Introduction, ]
featured: false
toc: true
summary: "Recap about a Docker workshop"
---

# Workshop

## A little context

At work I was very fortunate to participate in a introductory workshop about Docker made by my colleague Damien (thanks to him!). As consultant in the IT industry, it is our duty (and often our pleasure) to stay relevant in the field. To accomplish this goal, at work we have 2.5 hours per week dedicated to self-learning. It can be almost anything we want, as long as it helps us grow in our careers. This time, Damien had prepared a nice workshop for our team. 

Personally I had very few hands-on experiences with Docker prior to this workshop. My current project use Docker (and Kubernetes) but it was mostly a setup done once and is used to build the various environments we need our project to run on. Locally we installed everything ourselves, without relying on Docker at all (which is mostly because none of my teammates were comfortable with Docker either. If we had to start the project now, I think we might at least consider using Docker for our local development as well).

## Docker

Before talking about what we did, let's try to recap a bit what I know/understand about Docker.

Docker is a virtualization **container** running on top of the host operating system, like a lightweight VM. In that container, you define the OS to run, you install the dependencies for the application you are building, and then you package it.

Doing it that way allows to run the application anywhere that support Docker (maybe we can see it like the JVM, a layer on top of an OS to *write once, run anywhere*).

## Workshop

The base for the workshop was the [Docker Curriculum](https://docker-curriculum.com/#hello-world) and we stopped before the section on working with Docker on AWS. 

