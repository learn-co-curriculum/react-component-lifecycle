# React Component Lifecycle

## Objectives

1. Describe the phases of the React component lifecycle
2. Explain the importance of different phases of the lifecycle

## Overview

This lesson is a very high-level over view of the React component lifecycle. We
want to walk students through the whole shebang.

It might be helpful to break the cycle down into mounting (happens once),
rendering (happens a bazillion times), and unmounting (happens once) phases.



## Mounting

### componentWillMount

### componentDidMount


## Updating

### componentWillReceiveProps

### shouldComponentUpdate
**shouldComponentUpdate** is called every time the component is about to re-render, immediately before the `render()` method. It is not called on the initial render. The component's next props and next state get passed into the function and it will need to return a boolean value for whether or not a re-render should take place. Any update to the component's state or any change in any of its parent components could potentially trigger a re-render, so this method can be used to prevent unnecessary re-renders.

Suppose you have a React component which has a `name` prop and you want to re-render the component only if the `name` has changed. This is where the **shouldComponentUpdate** function becomes useful:

```

shouldComponentUpdate: function(nextProps, nextState){
  return this.props.name !== nextProps.name;
}
```

**NOTE:** you might think it'd be a good idea to use the `shouldComponentUpdate` function to only re-render the component if *any* of the props have changed and avoid *all* redundant re-renders, e.g. if `this.props !== nextProps`. However, because `props` and `nextProps` are both JavaScript objects, this comparison will always return `true`, that is `{} === {}` is never `true` in JavaScript (object equality is one of the many JavaScript quirks you'll need to know about. The reasons behind it are a bit too advanced to explain at this stage, it's enough just to know about it. Further reading [here](http://adripofjavascript.com/blog/drips/object-equality-in-javascript.html) if you're interested). There's ways to get around this restriction either by stringifying both objects or by doing a deep compare (compare each attribute of the object individually), but both of those operations are rather expensive, especially for big objects, and would be significantly more memory-intensive than an "unnecessary" re-render.


### componentWillUpdate

### componentDidUpdate


## Unmounting

### componentWillUnmount





## Resources

- [React: Component Specs and Lifecycle](https://facebook.github.io/react/docs/component-specs.html)
- [Understanding the React Component Lifecycle](http://busypeoples.github.io/post/react-component-lifecycle/)
