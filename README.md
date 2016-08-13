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
**componentWillMount** is called only once in the component lifecycle, immediately before the render method is executed. It is usually used to perform any state changes needed before the initial render, as calling `this.setState` in this method will not trigger another re-render despite the state change, so the rendering will only occur once. The use-cases for this are quite subtle, but for example, suppose you wanted to keep the dateTime of when the component was created in your component state, you could set this up in `componentWillMount`.

```
componentWillMount: function(){
  this.setState({ startDateTime: new Date(Date.now())})
}
```

### componentDidMount
**componentDidMount** is also only called once, immediately *after* the `render()` method has taken place. That means that the HTML for the React component has been rendered into the DOM and can be accessed if neccessary. Any DOM manipulation of data-fetching should take place in this method.

Suppose we were building a weather app which fetches current weather data and displays it to the user. We would want this data to update every 15 seconds without the user having to refresh the page. **componentDidMount** to the rescue!

```
componentDidMount: function(){
  this.interval = setInterval(this.fetchWeather, 15000);
}
```

Here, `fetchWeather()` is another function in the React component, which calls a weather API and updates the state using `this.setState()` which will trigger a re-render of the react component with the new state.

It is very important that if any DOM manipulation or asyncronous calls have been set up in this function, they are cleaned up later in `componentWillUnmount` (discussed below). Failing to do so will pollute the DOM and can cause unintented consequences, including even possibly crashing your carefully crafted website!

## Updating

### componentWillReceiveProps

### shouldComponentUpdate
**shouldComponentUpdate** is called every time the component is about to re-render, immediately before the `render()` method. It is not called on the initial render. The component's next props and next state get passed into the function and it will need to return a boolean value for whether or not a re-render should take place. Any update to the component's state or any change in any of its parent components could potentially trigger a re-render, so this method can be used to prevent unnecessary re-renders.

Suppose you have a React component which has a `temperature` prop and you want to re-render the component only if the `temperature` has changed. This is where the **shouldComponentUpdate** function comes in handy:

```

shouldComponentUpdate: function(nextProps, nextState){
  return this.props.temperature !== nextProps.temperature;
}
```

**NOTE:** you might think it'd be a good idea to use the `shouldComponentUpdate` function to only re-render the component if *any* of the props have changed and avoid *all* redundant re-renders, e.g. if `this.props !== nextProps`. However, because `props` and `nextProps` are both JavaScript objects, this comparison will always return `true`, that is `{} === {}` is never `true` in JavaScript (object equality is one of the many JavaScript quirks you'll need to know about. The reasons behind it are a bit too advanced to explain at this stage, it's enough just to be aware of it. Further reading [here](http://adripofjavascript.com/blog/drips/object-equality-in-javascript.html) if you're interested). There's ways to get around this restriction either by stringifying both objects or by doing a deep compare (compare each attribute of the object individually), but both of those operations are rather expensive, especially for big objects, and would be significantly more memory-intensive than an "unnecessary" re-render.


### componentWillUpdate

### componentDidUpdate


## Unmounting

### componentWillUnmount
**componentWillUnmount** is the last function to be called immediately before the component is removed from the DOM. It is generally used to perform clean-up for any DOM-elements or timers created in **componentWillMount**. Recall, for **componentWillMount** we set up a timer to re-fetch our weather data every 15 seconds. Before the React component is removed from the DOM, we would need to clean up after ourselved and remove the interval.

```
componentWillUnmount: function(){
  clearInterval(this.interval);
}
```





## Resources

- [React: Component Specs and Lifecycle](https://facebook.github.io/react/docs/component-specs.html)
- [Understanding the React Component Lifecycle](http://busypeoples.github.io/post/react-component-lifecycle/)
