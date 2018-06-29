# React Component Lifecycle

## Overview

In this lesson, we'll describe the phases, as well as the importance, of the
React component lifecycle.

## Objectives

1. Distinguish between props and states as they relate to React components
2. Describe the three phases of the React component lifecycle
3. Define lifecycle methods and how they enable the component to react to different events
4. Define and describe various lifecycle methods

## Component Lifecycle

React components have two sets of properties: **props** and **state**. Props are
given to the component by its parent. You can think of props as an external
influence that the component has no control over, whereas a component's state is
internal to the component. A component's state can change in conjunction to the
props changing or when the user interacts with the component.

The React framework was designed to enable developers to create complex and
highly reactive UIs. This enables the components to quickly adapt to changes
from user interactions or updates in the app. In order to enable this, React
components go through what we call a **component lifecycle**. This is broadly
divided into three parts: **creation**, **updating**, and **deletion**.

This means that every single thing you see in applications written in React is
actually a React component and/or a part of one! For example, if you open a new
chat window in a website written in React, a `ChatWindow` component is
**created**. As you are interacting with it and sending messages to your friends -
that's the **updating** part. And when you finally close the window, the React
component gets **deleted**.

It seems all pretty straightforward from the user's perspective, however as
you'll soon find out, there's a lot of stuff going on behind the scenes.

## Lifecycle Hooks and Rendering

In order to enable this quick reacting and updating, as a developer, you get
access to certain built-in events in the React component lifecycle called
**lifecycle hooks** or **lifecycle methods**. These are opportunities for you to
change how the component reacts (or doesn't react) to various changes in your
app.

These methods are called *lifecycle* methods, because they are called at
different times in the component's lifecycle - just before it's created, after
it's created, and when it's about to be deleted.

The only required method for a React component to be valid is the `render()`
method which describes what the HTML for the component looks like. There are a
whole host of optional methods you can use if you need more control over how the
component responds to change. The optional methods will be called if you include
definitions for them in a component. Otherwise, React will follow its default
behavior.

## Pre-mounting

It is important to remember that components, at their core, are just JS classes.
This means that even before mounting has begun, the class's `constructor`
function is called.

While the `constructor` is not related to mounting to the DOM, it is the first
function called upon the initialization of a component; this makes it useful for
creating an initial state for a component.

## Mounting

When the component is initially created, it gets mounted onto the DOM. It sounds
more complicated than it is: the component figures out its initial state and
renders its initial JSX onto the page. At the mounting stage, there are two
*lifecycle hooks* available to us: `static getDerivedStateFromProps`, and
`componentDidMount`.

After the `constructor` is called, `static getDerivedStateFromProps` will get
called just _before_ `render`. This method gives us access to any props and
state, and can modify and return state before a component is rendered. It is
uncommon that we use this hook - even [the React documentation][derived]
mentions that it exists for rare use cases. This method is called every time a
component renders. The initial render and all subsequent re-renders of content.

Since the introduction of React 16, this is the _only_ hook that fires before
render() during mounting and updating. Any code in this lifecycle method is extra
code to run _before_ JSX is rendered to the DOM.

After the `constructor` and `static getDerivedStateFromProps`, `render` is
invoked, most often returning JSX so that React can insert it into the DOM.

The `componentDidMount` method will get called just _after_ the `render` method.
You would use this method to set up any long-running processes or asynchronous
processes such as fetching and updating data. It is better to render and display
_something_ to your user even if all of your data isn't ready yet. Once it _is_
ready, React can just re-render and use the API content.

## Updating

Whenever a component's state or props are changed, it gets re-rendered on the
page. That's the beauty of React components - they're quick to *react* to
changes. A re-render could be triggered when a user interacts with the
component, or if new data (props or state) is passed in.

For example, going back to the chat window example, whenever you press "send" on
a message, the `ChatWindow` component gets re-rendered as it needs to display an
extra message. Whenever a re-render is triggered, there is a whole host of
lifecycle hooks that get called. You can choose to use any of these to decide
how your React component should respond to changes.

The `static getDerivedStateFromProps()` is invoked before anything else when
updating a component, and again, is available for rare situations where you may
need to calculate state changes prior to an update.

The `shouldComponentUpdate` method is invoked just before the component is about
to re-render. At this stage, you can compare the old and new props and state and
prevent unnecessary re-renders: if the changes in state and/or props don't
actually alter the component that's being shown to the user, there is no point
"repainting" it as it is an unnecessary performance drain.

After the first two hooks, `render` is called, returning the JSX for React.
React uses this JSX to figure out what to display on the page.

_Just_ before updating, `getSnapshotBeforeUpdate` is invoked. The ¥
`getSnapshotBeforeUpdate` method returns a 'snapshot' that can be used in the
final update lifecycle method, `componentDidUpdate`. This snapshot allows us to
capture things like scroll position. This sort of value can possibly change in
the small amount of time before `componentDidUpdate` is invoked.

The `componentDidUpdate` method is called just after the component is rendered
and updated. It is possible in `componentDidUpdate` to take some actions
without triggering a re-render of the component, such as focusing on a specific
form input.

You will have access to the previous props and state as well as the current
ones, and you can use this method to update any third party libraries if they
happen to need an update due to the re-render.

## Unmounting

At the unmounting stage, the component gets deleted and cleared out of the page.
The only lifecycle hook at this stage is `componentWillUnmount`, which is called
just before the component gets deleted. This is used to clear out any stuff set
up in `componentDidMount`.

For example, if you had a component that displays the weather data in your home
town, you might have set it up to re-fetch the updated weather information every
10 seconds in `componentDidMount`. When the component gets deleted, you wouldn't
want to continue doing this data-fetching, so you'd have to get rid of what was
set up in `componentWillUnmount`.

## Summary

Imagine a big old oak tree. The tree could be a parent component, each of its
branches a child component of the tree, each of its leaves a child component of
the branch and so on. Each of the leaves go through a very obvious lifecycle of
being created, changing based on state (changing colour based on the season,
withering if there's not enough nutrition being passed down from the parent
branch, changing into a leaf with a hole bitten out of it if a caterpillar
munches on it), and finally falling down when it's autumn.

So as it seems, if you need a lifecycle hook, there's sure to be one for your
every need!

#### Mounting lifecycle methods

Called once on initial render:

| Method            | current props and state | prevProps | prevState | nextProps |  nextState | Can call `this.setState` | Called when?               | Used for                                                                                    |
|:-------------------------:|:---------:|:---------:|:----------------------:|:-------------------------------------------------------:|:--------------------------------------------------------------------------------:|
| `constructor` |     no    |     no    |     no    |     no    |     no    |     no    | once, just before `static getDerivedStateFromProps()` is called for the first time | Setting initial state                                             |
| `static getDerivedStateFromProps()` |     yes    |     no    |     no    |     no    |     no    |     yes    | right before the initial render and **all** re-renders | Not used often |
| `render()`           |     yes   |     no    |     no    |     no    |     no    |     no    | every time React updates and commits to the DOM | Writing JSX for components |
| `componentDidMount`  |     yes   |     no    |     no    |     no    |     no    |     yes   | once, just after mounting  | setting up side effects (e.g. creating new DOM elements or setting up asynchronous functions |

#### Updating lifecycle methods

Not called on initial render, but always called whenever a subsequent re-render is triggered:

| Method            | current props and state | prevProps | prevState | nextProps |  nextState | Can call `this.setState` | Called when?               | Used for                                                                                    |
|:-------------------------:|:---------:|:---------:|:----------------------:|:-------------------------------------------------------:|:--------------------------------------------------------------------------------:|
| `static getDerivedStateFromProps()` |    yes    |     no    |     no    |     no    |     no    |     yes     |     before every render  |   Not used often |
|   `shouldComponentUpdate`   |    yes    |    no    |    no    |    yes    |    yes   |    yes    | before every re-render (not initially) | can be used to stop unnecessary re-renders for performance optimization |
|     `getSnapshotBeforeUpdate`    |    yes   |    yes   |    yes   |    no   |    no   |    yes   | just before React updates and commits new content to the DOM | used rarely; can capture data that may be changing rapidly |
|     `componentDidUpdate`    |    yes   |    yes    |    yes    |    no   |    no    |    yes    | just after a re-render has finished | any DOM updates following a render (mostly interacting with 3rd party libraries) |

Current props and state are always available through `this.props` and
`this.state`. Some of these methods have access to previous props and state, or
the next props and state. In these cases, the props and state are being passed
into the method by React.

#### Dismounting lifecycle method

Called only once, just before the component is removed from the DOM:

| Method            | current props and state | prevProps | prevState | nextProps |  nextState | Can call `this.setState` | Called when?               | Used for                                                                                    |
|:--------------------:|:---------:|:---------:|:----------------------:|:---------------------------------------------------:|:-------------------------------------------------------:|
| `componentWillUnmount` | yes | no | no | no | no | n/a | once, just before component is removed from the DOM | destroying any side effects set up in componentDidMount |



## Resources

- [React: Component Specs and Lifecycle](https://facebook.github.io/react/docs/component-specs.html)
- [Understanding the React Component Lifecycle](http://busypeoples.github.io/post/react-component-lifecycle/)

<p class='util--hide'>View <a href='https://learn.co/lessons/react-component-lifecycle'>Component Lifecycle</a> on Learn.co and start learning to code for free.</p>

[derived]: https://reactjs.org/docs/react-component.html#static-getderivedstatefromprops
