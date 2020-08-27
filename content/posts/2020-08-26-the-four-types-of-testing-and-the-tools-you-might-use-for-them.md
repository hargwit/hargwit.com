---
template: post
title: The four types of testing (and the tools you might use for them)
slug: the-four-types-of-testing
draft: true
date: 2020-08-26T07:13:03.202Z
description: There a four types of testing, here I explain how they work, what
  you might use them for and some tools you could use when testing a React
  application
category: Testing
tags:
  - React
  - testing
  - TDD
  - unit tests
  - integration tests
  - end-to-end tests
  - static tests
---
There are four different types of test you can (and should) use when writing code:

* Static
* Unit
* Integration
* End to end

There are two important metrics to consider when writing a test, feedback time and confidence.

## Feedback time

The feedback time of a test is how long that test takes to tell you that your code is working/broken. 

Imagine a test is failing because of a bug in the code, and you have an idea for a fix. You want to know in the shortest amount of time possible whether or not your changes fix the bug (make the test pass). If the test takes 5 minutes to run, then you will be waiting for 5 minutes before you know your fix worked. This is a long feedback time.

So you can see feedback time is essentially how long the test takes to run, and it's something we want to minimise.

## Confidence

Confidence is how confident the test makes us that the code is working.

Imagine you are refactoring a function in the authentication flow of your app to store passwords as hashes rather than plain text. After implementing the changes, how confident are you that a user can still log in to your system? A test that covers the authentication flow makes it an absolute certainty and gives you confidence that the codebase isn't broken.

Confidence is something we want to maximise.

## Trade off

As a general rule, slower tests give you more confidence that your code is working.