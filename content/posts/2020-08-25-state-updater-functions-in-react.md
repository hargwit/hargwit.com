---
template: post
title: State update functions in React
slug: react-state-update-functions
draft: false
date: 2019-09-09T08:00:00.000Z
description: Whenever you update state based on previous state, use an update
  function to avoid stale state problems.
category: React
tags:
  - React
  - state
  - asynchronous
  - async
---
Let's build a counter component as a React class component!

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  increment() {
    const { count } = this.state;
    this.setState({ count: count + 1 });
  }

  render() {
    const { count } = this.state;
    return (
      <>
        <p>
          <label id="count-label">
            Count: <span aria-label="count-label">{count}</span>
          </label>
        </p>
        <button onClick={() => this.increment()}>Increment</button>
      </>
    );
  }
}
```

Here we have a class component with a simple state containing only one variable
`count` whose value is displayed on `render`. Clicking the increment button
simply calls `setState` on the component which, you guessed it, increments the
`count` state variable. Checkout this [Codesandbox](https://codesandbox.io/s/simple-pf4u0) to have a play with this component.

All straightforward right? What you might not have noticed though is that we
have produced a difficult to trace and inconsistent bug.

Let's checkout the [React docs](https://reactjs.org/docs/react-component.html#setstate) for `setState`:

> "Think of `setState` as a request rather than an immediate command. For better
> perceived performance, React may delay it, and then update several components
> in a single pass."

This means that updating state in this way is an asynchronous process.

What does that mean for us? Well, what if we were to quickly click the button twice so
that the two `setState` operations get batched together into the same cycle?

We would find that the second call of `increment` would execute the line `const { count } = this.state`
**before** the state has been updated by the first click. The second execution
would increment the `count` variable based on *stale state* and hence `count`
would be set to the wrong value.

This bug can lead to some inconsistent behaviour depending on how quickly the
button is clicked. For example the following test clicks the button a number of
times and asserts that the correct value of `count` is displayed.

```jsx
test("Counter counts accurately", () => {
  render(<Counter />);

  expect(screen.getByLabelText(/Count/i)).toHaveTextContent(0);

  fireEvent.click(screen.getByText(/Increment/i));
  expect(screen.getByLabelText(/Count/i)).toHaveTextContent(1);

  fireEvent.click(screen.getByText(/Increment/i));
  expect(screen.getByLabelText(/Count/i)).toHaveTextContent(2);
});
```

We could run this test on a test environment in a CI pipeline and, depending on
the state of the environment and number of other tests running, this test might
run at different speeds. 

As a result, sometimes the `setState` calls might get
batched and sometimes they might not. Hence occasionally the test will fail with
no indication as to why - this might lead a developer to incorrectly believe the
test to be flaky when in fact it is highlighting a bug in the code!

You and I can't click as fast as a computer so let's add a delay between
obtaining `count` from state and setting the new state to better illustrate the
issue.

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  increment() {
    const { count } = this.state;
    setTimeout(() => {
      this.setState({ count: count + 1 });
    }, 1000);
  }
  render() {
    const { count } = this.state;
    return (
      <>
        <p>
          <label id="count-label">
            Count: <span aria-label="count-label">{count}</span>
          </label>
        </p>
        <button onClick={() => this.increment()}>Increment</button>
      </>
    );
  }
}
```

We have told `setTimeout` to increment our state after a 1 second delay. Go
ahead and checkout this [codesandbox](https://codesandbox.io/s/buggy-seovi?file=/src/Counter.js). Click the button twice within 1 second. We
would expect the state to increment by 2 but instead what we see is the state
only increment by one. This is because the two executions of `increment`
obtained the same value for `count` and so both set the state to the same value.

A bug! And an inconsistent bug at that as the code appears to work correctly if
the button is clicked more slowly.

So what can we do to fix the issue? Let's turn back to the
[React docs](https://reactjs.org/docs/react-component.html#setstate) - the
`setState` function has the following definition.

```
setState(updater[, callback])
```

Where `updater` is a function with the signature:

```
(state, props) => newState
```

In our case, the next state of the count variable only depends on the previous
so we won't use props. Let's put this in our code.

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  increment() {
    setTimeout(() => {
      this.setState((previousState) => ({
        count: previousState.count + 1
      }));
    }, 1000);
  }

  render() {
    const { count } = this.state;
    return (
      <>
        <p>
          <label id="count-label">
            Count: <span aria-label="count-label">{count}</span>
          </label>
        </p>
        <button onClick={() => this.increment()}>Increment</button>
      </>
    );
  }
}
```

Now, when `setState` is executed, we are guaranteed to have the current state
passed to our updater function, even when `setState` executions get batched. As
a result we never update `count` based on stale state and we have fixed the bug!
Try this [codesandbox](https://codesandbox.io/s/fixed-5lqjp?file=/src/Counter.js) for the final fixed component.

To avoid this, follow this general rule:

> **Whenever you update state based on previous state, use an update function to avoid stale state problems.**

# Conclusion

Updating the state of a component based it's old state can introduced difficult
to trace bugs if not done correctly. If two `setState` operations get batched
they may attempt to update the state based on the same previous value leading to
the incorrect final state.

The React docs tell us that `setState` can use an updater function whose
arguments are the current state and props of the component. This allows us to
reliably and confidently update the state of the component.

Thanks for reading! Hopefully now you have an understanding of how to correctly
use `setState` and can recognise a bug in your codebase you didn't realise was
there!