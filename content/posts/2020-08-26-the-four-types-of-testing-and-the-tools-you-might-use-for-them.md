---
template: post
title: The four types of testing and when to use them
slug: the-four-types-of-testing
draft: false
date: 2020-09-11T06:32:57.025Z
description: There are four types of test. Here I explain how, and when, to use
  them, including example tools for a React application.
category: Testing
tags:
  - React
  - testing
  - TDD
  - unit testing
  - integration testing
  - end-to-end testing
  - static testing
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

Static testing, also called linting, is the process of running a program that will statically analyse code for errors. This is possible as many of the bugs you might inadvertently create while writing code are very common and easy to recognise. 

This includes silly mistakes like typos in variable names, forgotten brackets and mismatched types, to name a few. The linter can scan the code as you type, and raise a warning when it thinks you have made a mistake.

Static testing is the fastest type of testing, providing an almost instantaneous feedback time. However, it will only give you confidence that your code runs, not that your latest feature meets its requirements

The most common static testing tool for javascript applications is `eslint`. `eslint` is pluggable, meaning rule sets specific to your tech stack can be installed and plugged into the linter. You can also provide configuration to ignore some errors or force developers to use best practices.

# Unit testing

This is probably the kind of testing you've heard about before because your teacher told you you had to write them.

Unit tests ensure each singular element of an application is working correctly. Whether that's a function, method, class or component, unit tests confirm that they are doing their one thing well.

Unit tests should be fast, taking only a few seconds to run. This gives them a short feedback loop while also giving you much more confidence that your code is working over static testing.

For a React application, I would recommend using [`jest`](https://jestjs.io/) and [`react-testing-library`](https://testing-library.com/docs/react-testing-library/intro). Combining these libraries allows you test your functions, component classes and methods in the same way your users will be using them.

However, just because your unit tests pass, it doesn't mean the application as a whole is working:

![a gif of two automatic doors causing each other to open as they are too close](/media/unit-test-no-integration.gif "Unit tests done! That's enough testing!")

# Integration tests

Alright, you've finished your unit tests and you're confident that the individual elements of your app are working. Awesome! But it's not enough. We need to know that the units can work together. We need to combine our units and test that our integration works as expected.

We might test that a service method works correctly when its repositories are connected to real databases. Or test an entire page of our application - combining many individually tested components.

Integration tests cover a lot more code and even check that the interfaces between the layers (eg. networks) aren't broken. They tend to be covering the use-cases of the application and this skyrockets the confidence they give us. After all, we are building the application to meet those use-cases.

However, integration tests execute more code and can be slowed down by the interfaces between layers. This makes integration tests slower than their unit counterparts.

Use the same tools for integration tests as you did for unit testing - a combination of [`jest`](https://jestjs.io/) and [`react-testing-library`](https://testing-library.com/docs/react-testing-library/intro).

# End to end tests

End-to-end tests are as close as you can get to testing your application as an actual user. These tests point a browser at your application and click, type and interact with elements on the page exactly as the user does. 

End-to-end tests run against the entire system, stood up in an environment just as it will be in live. This gives you ultimate confidence that your application is working as expected.

However, this confidence comes at a cost. There are no shortcuts here, the test must wait for every network request and database transaction to finish. End-to-end tests take minutes to run rather than seconds.

As a result, end-to-end tests should be reserved for mission critical features. These are features where a breakage makes the application useless for its primary use-cases. For example, login and sign up flows and payment gateways.

Use [`cypress`](https://cypress.io) combined with [`cypress-testing-library`](https://testing-library.com/docs/cypress-testing-library/intro) for end-to-end testing a React application. `cypress` can be run both in CI pipelines as well as on your local dev machine making it a great option for TDD. `cypress-testing-library` is a `cypress` port of `react-testing-library` meaning it will feel familiar due to your unit and integration tests.

# Trade off

As a general rule, the more confidence a test gives us, the slower it is - there is a tradeoff between the two metrics. 

We need to be careful and consider how important these metrics are when we write a new test. Is the new feature mission-critical (eg. authentication flow)? If so then we must be confident the code works - let's use an end to end test. If not (eg. user can add emoji to their username) then let's use something faster - a unit test probably.

When in doubt, use integration tests, they provide a lot of confidence and are nearly as fast as unit tests to run. Follow Kent C. Dodds advice:
> Write tests, not too many, mostly integration

Thanks for reading, get testing!