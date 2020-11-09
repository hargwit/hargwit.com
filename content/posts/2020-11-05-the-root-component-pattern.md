---
template: post
title: The Root Component Pattern
slug: root-components
draft: true
date: 2020-11-05T08:37:42.818Z
description: Learn how to set the root type of a component via a prop. A pattern
  seen in many popular libraries including material-ui.
category: React
tags:
  - React
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
/>
```
