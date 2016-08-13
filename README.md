# React Component Lifecycle

## Objectives

1. Describe the phases of the React component lifecycle
2. Explain the importance of different phases of the lifecycle

## Overview

The `render()` method, which returns the actual code to render into the DOM, is the only mandatory built-in method for React compoments. However, there are a hole host of other lifecycle methods which are pre-configured to run at specific times in the component lifecycle and you can hook into as many or as few of these as you like. These methods are called on initial render, when new props are received or when the component has just finished updating etc. They are designed for you to enable to perform any desired state or DOM manipulations at certain times in the component lifecycle. There are best practices for what kind of actions you could (should) be performing in which method and we will go through all of these in detail.

The React lifecycle methods fall into 3 categories: Mounting, Rendering and Unmounting. The Mounting and Unmounting methods are only called once, when the component is first created and drestroyed respectively. The Updating methods are never called on initial render, but are called on every subsequent one until the Unmounting.

## Mounting
These methods are only called once when the compoment is initially created.

### componentWillMount
**componentWillMount** is called only once in the component lifecycle, immediately before the render method is executed. It is usually used to perform any state changes needed before the initial render, because calling `this.setState` in this method will not trigger another re-render despite the state change, so the rendering will only occur once. The use-cases for this are quite subtle, but for example, suppose you wanted to keep the dateTime of when the component was created in your component state, you could set this up in `componentWillMount`.

```
componentWillMount: function(){
  this.setState({ startDateTime: new Date(Date.now())});
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

Here, `fetchWeather()` is another function in the React component, which calls a weather API and updates the state using `this.setState()` which will trigger a re-render of the React component with the new state.

It is vital that if any DOM manipulation or asyncronous calls have been set up in this function, they are cleaned up later in `componentWillUnmount` (discussed below). Failing to do so will pollute the DOM and can cause unintented consequences, including even possibly crashing your carefully crafted website!

## Updating
These methods are called every time a re-render is triggered.

### componentWillReceiveProps
**componentWillReceiveProps** is invoked when the component is receiving new props. This function is not called in the initial render. As with **componentWillMount**, calling `this.setState` here does not trigger an additional re-render, so this function is primarily used for any state changes necessary due to receiving new props.

```
componentWillReceiveProps: function(nextProps){
  this.setState({ temeratureIncreasing: nextProps.temperature > this.props.temperature })
}
```

It should be noted that there is no equivalent method for state changes such as **componentWillReceiveState**, because a new prop might trigger a state change, but the opposite can never be true - a component can never mutate its own props. If any actions are required to be performed in reponse to state change, this can be done in **componentWillUpdate**.

### shouldComponentUpdate
**shouldComponentUpdate** is called every time the component is about to re-render, immediately before the `render()` method. It is not called on the initial render. The component's next props and next state get passed into the function and it will need to return a boolean value for whether or not a re-render should take place. Any update to the component's state or any change in any of its parent components could potentially trigger a re-render, so this method can be used to prevent unnecessary re-renders.

Suppose you have a React component which has a `temperature` prop and you want to re-render the component only if the `temperature` has changed. This is where the **shouldComponentUpdate** function comes in handy:

```
shouldComponentUpdate: function(nextProps, nextState){
  return this.props.temperature !== nextProps.temperature;
}
```

**NOTE:** you might think it'd be a good idea to use the `shouldComponentUpdate` function to only re-render the component if *any* of the props have changed and avoid *all* redundant re-renders, e.g. if `this.props !== nextProps`. However, because `props` and `nextProps` are both JavaScript objects, this comparison will always return `true`, that is `{} === {}` is never `true` in JavaScript (object equality is one of the many JavaScript quirks you'll need to know about as a developer. The reasons behind it are a bit too advanced to explain at this stage, it's enough just to be aware of it. Further reading [here](http://adripofjavascript.com/blog/drips/object-equality-in-javascript.html) if you're interested). There's ways to get around this restriction either by stringifying both objects or by doing a deep compare (compare each attribute of the object individually), but both of those operations are rather expensive, especially for big objects, and would be significantly more memory-intensive than an "unnecessary" re-render.


### componentWillUpdate
**componentWillUpdate** is called immediately after **shouldComponentUpdate** returns true. No state changes are allowed in this method and it should be used solely for preparing for the upcoming update, not trigger one. One of the more common uses of **componentWillUpdate** is to to call an action, set a variable or start an animation (not in the state) based on state changes. For example, the code below will dispatch some action based on a state change.

```
componentWillUpdate(nextProps, nextState) {
  if (nextState.open === true && this.state.open === false) {
    this.props.onOpen();
  }
}
```

### componentDidUpdate
**componentDidUpdate** is called immediately after the re-render has occurred. The arguments passed into the function is slightly different: we get access to `prevProps` and `prevState` as the arguments passed into **componentWillUpdate**, `nextProps` and `nextState` have become the current props and state and can be accessed with `this.props` and `this.state`. **componentDidUpdate** is used to manipulate the DOM following the render. Calling this.setState is allowed in **componentDidUpdate**, but err on the side of caution, as you can easily end in an infinite loop of re-renders.

**componentDidUpdate** isn't used an awful lot in everyday development, but could be used with interacting with third party libraries when they need an update due to the component re-render (e.g. new data for charting libraries).

```
componentDidUpdate(prevProps, prevState) {
  if (prevProps.height !== this.props.height) {
    someChartLibrary.updateHeight(this.props.height);
  }
}
```


## Unmounting
This method is called only once, just before the component is removed.

### componentWillUnmount
**componentWillUnmount** is the last function to be called immediately before the component is removed from the DOM. It is generally used to perform clean-up for any DOM-elements or timers created in **componentWillMount**. Recall, for **componentWillMount** we set up a timer to re-fetch our weather data every 15 seconds. Before the React component is removed from the DOM, we would need to clean up after ourselved and remove the interval.

```
componentWillUnmount: function(){
  clearInterval(this.interval);
}
```


## Summary
If you need a lifecycle hook, there's one for your every need! Even though the React framework doesn't necessarily force you to use them as described above, but it's generally good practice to use them as they were intended as doing otherwise may lead to undesirable consequences.

### Mounting lifecycle methods
Called once on initial render:

| Method             | nextProps | nextState | can call this.setState | called when?               | used for                                                                                    |
|--------------------|:---------:|:---------:|:----------------------:|:--------------------------:|:-------------------------------------------------------------------------------------------:|
| **componentWillMount** |     no    |     no    |           yes          | once, just before mounting | setting initial state based on props                                                        |
| **componentDidMount**  |     no    |     no    |           no           | once, just after monuting  | setting up side effects (e.g. creating new DOM elements or setting up asyncronous functions |


### Updating lifecycle methods
Not called on initial render, but always called whenever a subsequent re-render is triggered:

|           Method          | nextProps | nextState | can call this.setState |                       called when?                      |                                     used for                                     |
|:-------------------------:|:---------:|:---------:|:----------------------:|:-------------------------------------------------------:|:--------------------------------------------------------------------------------:|
| **componentWillReceiveProps** |    yes    |     no    |           yes          |  many times, whenever component is receiving new props  |                     applying state changes based on new props                    |
|   **shouldComponentUpdate**   |    yes    |    yes    |           no           |    many times, whenver a rerender has been triggered    |    deciding based on new & old props & state whether a re-render should occur    |
|    **componentWillUpdate**    |    yes    |    yes    |           no           | many times, when new state and props are being received | prepare for the update, dispatch any actions or animations based on state change |
|     **componentDidUpdate**    |    yes*   |    yes*   |           yes          |    many times, just after the re-render has finished    | any DOM updates folloring a render (mostly interacting with 3rd party libraries) |

\* **componentDidUpdate** will actually receive `prevProps` and `prevState` as arguments, as the newly applied state and props can be accessed through `this.props` and `this.state`.


### Dismounting lifecycle method
Called only once, just before the component is removed form the DOM:

|        Method        | nextProps | nextState | can call this.setState |                     called when?                    |                         used for                        |
|:--------------------:|:---------:|:---------:|:----------------------:|:---------------------------------------------------:|:-------------------------------------------------------:|
| **componentWillUnmount** |     no    |     no    |           no           | once, just before component is removed form the DOM | destroying and side effects set up in componentDidMount |

## Resources

- [React: Component Specs and Lifecycle](https://facebook.github.io/react/docs/component-specs.html)
- [Understanding the React Component Lifecycle](http://busypeoples.github.io/post/react-component-lifecycle/)
