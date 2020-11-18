---
template: post
title: Programming to an interface with typescript
slug: programming-to-an-interface-with-typescript
draft: true
date: 2020-11-16T08:11:24.379Z
description: Programming to an interface is a surefire way to increase the value
  of your code. Let's explore how we can do this with typescript on a node
  project using the factory pattern.
category: Node
tags:
  - Typescript
  - Node
  - Programming to an interface
---
Let's start by loosely defining an interface.

An interface is essentially a contract; it describes a series of functions and their input and output types, without mentioning any specifics regarding how they work. Interfaces are about **what** rather than *how*.

This separation of **what** and *how*, of **interface** and *implementation*, allows for total decoupling between the code using (calling) the interface and the implementation details it hides. Decoupling these concerns makes the code cleaner, and therefore increases its value.

Let's explore a real-life example; say we are building a chat application and that our first feature is allowing a user to create a new chat. We will need to be able to persist chats, whether that means saving them to a database or writing them to a file, we don't care so long as we can save them somewhere.

When programming to an interface, we want to specify that this is possible, not how it is possible:

```
interface ChatRepository {
  create(chat: Chat): Promise<Chat>
}
```

In this example, we have used a typescript `interface` to describe what the user of the interface can do. The interface specifies a `create` method that takes as its input a new `Chat`, and returns a promise that resolves to the saved `Chat`.

Notice that there are no implementation details present; the interface tells us only **what** we can do, not *how* it happens.

# Implementations

Let's create a couple of implementations for the interface, starting with MongoDB. We'll do this using the factory pattern to create an instance of our repository.

```
const mongoChatRepositoryFactory({
  model
} : {
  model: mongoose.Model<Chat & mongoose.Document>
}) : ChatRepository => ({
  create: (chat: Chat) => model.create(chat).then(chatFactory)
}) 
```

Notice the signature of the factory function; the inputs are mongo specific arguments, while the output is a `ChatRepository`, the interface we defined earlier.

Returning the interface type means Typescript will force the object returned from the factory to exactly match the `ChatRepository`. If we miss any of the functions, Typescript throws an error. Therefore, Typescript guarantees that the mongo implementation provides all the functionality declared by the interface.

Now let's create another implementation, this time in memory.

```
const inmemChatRepositoryFactory = (): ChatRepository => {
    const chats = {}

    return {
        create: (chat: Chat) => {
            chats[chat.id] = chats
            return Promise.resolve(chat)
        },
    }
}
```

This time, the repository stores the chats in an object rather than a database. Notice again, though, how the factory returns the `ChatRepository` interface. We are using Typescript to again guarantee that the implementation provides all functionality declared by the interface.

# Usage

When programming to an interface, any service that uses the repository must interact only with the interface. For example:

```
interface ChatUseCases {
    create: (people: Person[], chatName: string) => Promise<Chat>
}

export const chatServiceFactory = ({ chatRepository }: { chatRepository: ChatRepository }): ChatUseCases => ({
    create: (people, chatName) => chatRepository.create(chatFactory({ participants: people, name: chatName })),
})
```

As we are now in the application layer of the system, every method on the service level interface is a use-case, hence `ChatUseCases`. Currently, we only have one use-case: create a chat from its participants and its name.

We can see that the implementation of the `ChatUseCases`, the `chatService`, takes a `ChatRepository` as an argument. Notice that this is the repository interface and not one of our implementations. The service doesn't know or care which implementation it uses, so long as it can create a Chat in a repository. It doesn't care about *how*, only **what**. The service is therefore completely decoupled from the repository implementation.

To convince you that this decoupling is highly beneficial, consider the alternative. Say we had allowed implementation details to leak from the repository layer into the service, we would then have Mongo specific code mixed in with our business logic. If we wanted later to swap out our database for say SQL or in-memory, we would need to rewrite our business logic in order to tear out the Mongo details. As a result, we would require more code changes and more testing to ensure the system still works, costing more time and money.

Decoupling means that this change is as simple as passing a different implementation into the the `chatServiceFactory`. We can even go one step further and switch implementation based on a CLI flag or config property:

```
let chatRepository: ChatRepository

if (inMem) {
    chatRepository = inMemChatRepositoryFactory()
} else {
    chatRepository = mongoChatRepositoryFactory({ model: ChatModel })
}

const chatService = chatServiceFactory({ chatRepository })
```
