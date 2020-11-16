---
template: post
title: The root component pattern
slug: root-components
draft: false
date: 2020-11-05T08:37:42.818Z
description: Learn how to set the root type of a component via a prop. A pattern
  seen in many popular libraries including material-ui.
category: React
tags:
  - React
  - React context
  - React.createElement
  - React patterns
---
If you have used the React material-ui library, you might already be familiar with the root component pattern. For example:

```
<Grid
  container
  component={MyComponent}
  myProp={"forwarded on to MyComponent"}
>
```

Here, the component prop on the `Grid` component accepts an element type (defaulted to `div`), to render as the root component of the grid. The `Grid` component forwards any props onto `MyComponent`.

This pattern effectively extends `MyComponent` with the functionality provided by `Grid`. Most of the use cases for setting the root component are presentational, for example, you might want a `Paper` component that has grid-like properties:

```
<Paper
  // Paper props
  variant="outlined"
  component={Grid}
  // Grid props
  container
  spacing={1}
/>
```

Or perhaps you would like the ability to set the underlying stying for a custom provider:

```
<ToDoProvider
  // ToDo props
  toDos={toDos}
  component={Paper}
  // Paper props
  variant="outline"
>
  // ...Components that need toDos
</ToDoProvider
```

Let's dive a little deeper into that last example to explore how we can implement this pattern, starting with a basic provider template:

```
import React from "react";

const ToDoContext = React.createContext(null);

const ToDoProvider = ({ toDos, children }) => (
  <ToDoContext.Provider value={toDos}>{children}</ToDoContext.Provider>
);

export { ToDoProvider };
```

Hopefully, you are already familiar with this pattern; we have created a React context, implemented a custom provider which renders the context provider and set the provider value to `toDos`. Now all `children` will be able to access the `toDos` either through a custom hook, or a consumer component.

Next, let's implement the root component pattern. This pattern hinges on the `React.createElement` function. Remember that `JSX`, the code you write between the `< >`, is just syntactic sugar over the top of `React.createElement`.  For example, if we put the following code through [Babel repl](https://babeljs.io/repl)

```
<div className="full-width">
  Hello World!
</div>
```

then the output is

```
React.createElement("div", {
  className: "full-width"
}, "Hello World!");
```

`React.createElement` has the following signature (taken straight from the [React docs](https://reactjs.org/docs/react-api.html#createelement)):

```
React.createElement(
  type,
  [props],
  [...children]
)
```

And we can see that this is the case in our earlier example; the `JSX` had `type` `div`, a single prop called `className` and only one child, the string `"Hello World!"`.

Now that we know how to use `React.createElement` we can finish off our component. First, we'll take the component type as a prop, destructuring any other props the provider needs and collecting the rest into a variable called `passThroughProps`. We will then return the output of `React.createElement`, passing our earlier render as `children` of the element.

```
import React from "react";

const ToDoContext = React.createContext(null);

const ToDoProvider = ({ toDos, component, children, ...passThroughProps }) =>
  React.createElement(
    component,
    passThroughProps,
    <ToDoContext.Provider value={toDos}>{children}</ToDoContext.Provider>
  );

export { ToDoProvider };
```

And that's it. Now we can render any component we like as the root of the `ToDoProvider`.

```
<ToDoProvider
  // ToDo props
  toDos={toDos}
  component={Paper}
  // Paper props
  variant="outline"
>
  // ...Components that need toDos
</ToDoProvider
```

I hope you find this pattern useful, or at least now understand how libraries like `material-ui` work under the hood.

Thanks for reading!