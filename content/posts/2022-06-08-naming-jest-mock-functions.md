---
template: post
title: Naming jest mock functions
slug: naming-jest-mock-functions
draft: false
date: 2022-06-08T11:03:07.882Z
description: A quick tip to add more context to test failure messages!
category: Testing
tags:
  - TIL
  - Testing
---
You may already be familiar with `jest.fn()`. We can use it to provide mocked implementations of functions in our tests and assert how many times they have been called and what with.

But what you may not know is that you can also set the name of that mock to provide some added context to your error messages when the tests do fail.

Consider the test below

```ts
test("should not fail", () => {
    const mock = jest.fn()

    expect(mock).toHaveBeenCalled()
})
```

When this test fails we are presented with the following error message

```log
expect(jest.fn()).toHaveBeenCalled()
```

If this were a more complicated test with more than one mocked function then this isn't actually a terribly helpful error message as it doesn't tell us *which* mock was not called.

Let's improve this test by adding some much needed context using the `mockName` function

```ts
test("should not fail", () => {
    const mock = jest.fn().mockName("myMockedFunction")

    expect(mock).toHaveBeenCalled()
})
```

Now when the test fails we get the following

```log
expect(myMockedFunctionn).toHaveBeenCalled()
```

Now our error message is much clearer and will help us get to the bottom of the issue faster!

If you want to have more a play checkout this [codesandbox](https://codesandbox.io/s/jest-fn-mockname-mnn4xi?file=/src/mockName.test.ts).

Thanks for reading!