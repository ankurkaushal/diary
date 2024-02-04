---
title: What I learned about React Hooks?
date: "2020-08-15"
description: What I learned about React Hooks?
---

Having recently finished a course on React Hooks, I wanted to recap some things myself.

What is it about Hooks that makes them special? Before discussing that, we need to discuss what is wrong with `class` based implementation first:

- Any time you need to use `props` in the code, you need to call `super(props)` in the constructor. 
- Before classes, React would auto bind all the functions when you used `createClass` whereas, with classes, you need to bind the functions with `this` in the constructor.
- Sharing non-visual logic required patterns like Higher Order Components or Render Props but they can get cumbersome & it can make it harder to understand what's going on.

With Hooks, all of those problems are solved since we are fundamentally using functional components but with a twist. 

So what can Hooks do? Hooks let you add state to the functional components. Simple.

But using Hooks requires us to unlearn what we learned about component lifecycles when we use classes. Instead of thinking in terms of component lifecycles, we should think in terms of synchronization. To synchronize pieces outside React with pieces inside React, React provides us with a very handy hook called `useEffect` hook. The `useEffect` hook lets you add side effects to your application. In Computer Science, [side effect](https://en.wikipedia.org/wiki/Side_effect_%28computer_science%29) is something that has an observable effect outside.

Apart from adding state & side effects to your application, hooks let you persist value across renders as well.

Core Hooks:

# useState

Lets you add state to the functional component. `useState` returns an array with two items, 1st item in the array is the initial state value & 2nd item is the function to update that value.

When to use `useEffect` hook? Use it for non-trivial state pieces.

# useReducer

This hook allows you to add state to a functional component using the `reducer` pattern. The API is similar to `.reduce` which accepts a reducer function & initial value for the function. The hook returns an array with the first item being state & the second item being the dispatch function to invoke the reducer.

# useEffect

Use it for doing any side effect work. Side effects include updating state variables, API calls, DOM manipulation, etc.

`useEffect` takes in a function that runs after every render & an optional dependency array. 

The dependency array can be:

- Absent, making the effect run on every render
- An empty array, making the effect run on initial render only.
- An array of dependent variables. Whenever the value of those variables changes, the effect will run. 

Sometimes you want to clean up effects on component unmount, to do that you can return a function inside the hook to clean up those effects.

# useRef 

Use this hook to add `ref` to any item in DOM. The hook returns a `ref` object with `current` property that's mutable & can be used in place of instance variables as well.

# useContext

This hook accepts a context object (the value returned from React.createContext) and returns the current context value for that context. The current context value is determined by the value prop of the nearest `<MyContext.Provider>` above the calling component in the tree.

# React.memo, useCallback, and useMemo

Use these hooks to memoize expensive renders. 