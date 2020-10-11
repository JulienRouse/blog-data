---
title: 'TODO'
url: '/blog/project_filesharing'
date: Tue, 16 Jun 2020 19:00:00 +0400
draft: true
tags: [circleci, continuous-integration]
featured: false
toc: true
summary: 'todo'
---

# Project filesharing

## scope

Through a web interface, being able to download/upload files, to have them accessible anywhere you have internet (pretty much a POC of dropbox).

Only one aws region

a rough web interface: a bit of html / js, maybe jquery or something

## technologies

- aws s3 (maybe in the future the ability to choose the storage?) / also s3 for a simple static website?
- aws EC2 / lambda depending what is possible
- aws IAM
- Python: flask or pyramid
- I probably need something like cloudfront / route 53 to handle the incoming traffic, not sure of that

## problems

- If s3 is used to store things, may I use one bucket per user, with the permissions dealt with aws IAM?
  Or do I need 1 big bucket and I deal with the permission myself? (say separating user by having a folder by user)
  The former seems cleaner but I don't know if it is possible (I presume it is though)
