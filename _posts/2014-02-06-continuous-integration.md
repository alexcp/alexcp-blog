---
layout: post
title: Continuous Integration
category: test
tag: blog
---

Having a good test suite with thorough coverage is a crucial part in any software project.
Unfortunatly, not every project have one, even if you do, obviously, you need to run it, and run it a lot, at least every time you commit. But As the test suite grows in size, it can become pretty long to run, especially if you have a lot of integration test. 

A Slow test suite can quickly become a burden. Waiting to get test results is a huge waste of time and kills productivity. It can get to a point where the team doesn't even bother anymore with running the test suite. Especially when doing quick fixes, but we all know that from time to time these quick fix can yield unexpected results.

A continuous integration server running your test suite every time something gets push, make you and your team confident.
It will protect you from all the small bugs that sometimes manage to sneak in and cause confusion an time lost.
A CI server will also greatly improves the managment of pull request and will save your team a lot of time by filtering out early the good from the bad.

### Solutions

There are a lot of availaible solutions to choose from.


[Travis-ci](http://travis-ci.org) is great and the awesome part is that they offer a free plan for open source project, so if developping an open source project, the choice is easy. However, there paid plans are really expensive, starting at 129$ a month.

I've used [Jenkins](http://jenkins-ci.org) in the past, it's a good product, but the configuration can be quite painful and you need to manage and pay for an extra server.

I'm currently using [Circle Ci](https://circleci.com) and I couldn't be happier. Configuration is a breeze, you can be up and running in about 2 minutesm the github integration is great and the price, starting at 19$/month, is excellent in my opinion.

There are a few other online services availaible, but I don't have experience with them. [Codeship](https://www.codeship.io/#pricing) and [Bamboo](https://www.atlassian.com/software/bamboo) for example.
