---
layout: post
title: Continuous Integration
category: test
tag: blog
---

Having a good test suite with thorough coverage is a crucial part in any software project.
Unfortunatly, not every project have one, even if you do, obviously, you need to run it, and run it a lot, at least every time you commit. But As the test suite grows in size, it can become pretty long to run, especially if you have a lot of integration test. 

A Slow test suite can quickly become a burden. Having to wait minutes to get the test results is a huge waste of time and kills productivity. It can get to a point where the team doesn't even bother anymore with running the test suite. Especially when doing quick fixes, but we all know that from time to time these quick fix can yield unexpected results.

A continuous integration server running your test suite every time something gets push, make you and your team confident.
It will protect you from all the small bugs that sometimes manage to sneak in.
It greatly improves the managment of pull request and will save your team a lot of time by filtering out early the good from the bad.

### Solutions

There are a lot of availaible solutions to choose from.
[Travis-ci](http://travis-ci.org) is great and what is even more awesome is that they offer a free plan for open source project, so if developping an open source project, the choice is easy. However, there paid plans are really expensive, starting at 129$ a month.

note:
[note](http://www.thoughtworks.com/continuous-integration)

There are a lot of other online services availaible that are more affordable, [circle-ci](https://circleci.com), [Codeship](https://www.codeship.io/#pricing) and [Bamboo](https://www.atlassian.com/software/bamboo) to name a few.
