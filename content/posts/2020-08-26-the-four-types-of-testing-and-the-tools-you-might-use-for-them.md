---
template: post
title: The four types of testing and when to use them
slug: the-four-types-of-testing
draft: true
date: 2020-08-26T07:13:03.202Z
description: There are four types of test. Here I explain how, and when, to use
  them, including example tools for a React application.
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

When deciding on which type of test to use, there are two important metrics to be considered, feedback time and confidence.

## Feedback time

The feedback time of a test is how long that test takes to tell you that your code is working/broken. 

Imagine a test is failing because of a bug in the code, and you have an idea for a fix. You want to know in the shortest amount of time possible whether or not your changes fix the bug (make the test pass). If the test takes 5 minutes to run, then you will be waiting for 5 minutes before you know your fix worked. This is a long feedback time.

So you can see feedback time is essentially how long the test takes to run, and it's something we want to minimise.

## Confidence

The confidence of a test is how confident that test makes us that the code is working.

Imagine you are refactoring a function in the authentication flow of your app to store passwords as hashes rather than plain text. After implementing the changes, how confident are you that a user can still log in to your system? A test that covers the authentication flow makes it an absolute certainty and gives you confidence that the codebase isn't broken.

Confidence is something we want to maximise.

# Static testing

Static testing, also called linting, is the process of running a program that will statically analyse code for errors. This is possible as many of the bugs you might create while writing code are very common and easy to recognise. 

This includes silly mistakes like typos in variable names, forgotten brackets and mismatched types, to name a few. The linter can scan the code as you type, and raise a warning when it thinks you have made a mistake.

Static testing is the fastest type of testing, providing an almost instantaneous feedback time. However, it will only give you confidence that your code runs, not that your latest feature meets its requirements

The most common static testing tool for javascript applications is `eslint`. `Eslint` is pluggable, meaning rule sets specific to your tech stack can be installed and plugged into the linter. You can also provide configuration to ignore some errors or force developers to use best practices.

# Unit testing

This is probably the kind of testing you've heard about before because your teacher told you you had to write them.

Unit tests ensure each element of an application is working correctly. Whether that's functions, methods, classes or components unit tests confirm that they are behaving as expected.

Unit tests should be fast, taking only a few seconds to run. This gives them a short feedback loop while also giving you much more confidence that your code is working over static testing.

However, just because your unit tests pass, it doesn't mean the application as a whole is working:

![a gif of two automatic doors causing each other to open as they are too close](/media/unit-test-no-integration.gif "Unit tests done! That's enough testing!")

# Trade off

As a general rule, the more confidence a test gives us, the slower it is -there is a tradeoff between the two metrics. 

We need to be careful and consider how important these metrics are when we write a new test. Is the new feature mission-critical (eg. authentication flow)? If so then we must be confident the code works - let's use an end to end test. If not (eg. user can add emoji to their username) then let's use something faster - a unit test probably.