---
template: post
title: Programming to an interface with typescript
slug: programming-to-an-interface-with-typescript
draft: true
date: 2020-11-16T08:11:24.379Z
description: Programming to an interface is a surefire way to increase the value
  of your code. Let's explore how we can do this with typescript on a node
  project.
category: Node
tags:
  - Typescript
  - Node
  - Programming to an interface
---
Let's start by loosely defining an interface.

An interface is essentially a contract; it describes a series of functions and their input and output types, without mentioning any specifics regarding how they work. Interfaces are about what rather than how.

Let's explore a real-life example; say we are building a chat application. Our first feature might be to allow a user to create a new chat. When programming to an interface, we want to specify that this is possible, not how it is possible:

```
interface ChatCreator {
  create(people: People[], chatName: string): Promise<Chat>
}
```

In this example, we have used a typescript `interface` to describe what the user of the system can do; this is a use-case of the system. The method specifies that its input is an array of people and that its output is a promise to resolve the newly create chat. The method also takes the name of the chat as a string.