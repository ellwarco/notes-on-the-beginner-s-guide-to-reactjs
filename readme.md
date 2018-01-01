# My notes on - 'Beginners guide to React'

⚠️ *Currently I'm still following the course, and with taking notes and all it's not going too fast! Keep an eye open for updates*

The more I use React, the more I want to find out what is actually happening under the hood. There is no shortage of React tutorials but most of them show you how to make something with React, but not how React does these things. Luckily there are some great resources available that do get to the core of things, one of which is the [‘Beginners guide to React’](https://egghead.io/courses/the-beginner-s-guide-to-reactjs) course from [Kent C. Dodds](https://twitter.com/kentcdodds) on egghead.io.

Besides the egghead.io course, I’ve watched [one of Kent’s talks](https://youtu.be/pugPxYH96TU) in which he covers much of the same grounds but goes a bit more in-depth at times.

During the course I also found the [React documentation](https://reactjs.org/docs/) to be really helpful, as the [React Enlightenment](https://www.reactenlightenment.com/) book which is available online for free and greatly compliments Kent's course.

At the bottom of this document you can find a list of resources I've been using throughout this course and underneath every chapter there's a list of resources about the chapter topic.

## Table of contents

1. [React Nodes](/react-nodes.md)
1. [JSX](/react-jsx.md)
1. React Components
    1. [Components - Creating a React component](/react-components-creating-a-component.md)
    1. [Components - An introduction to prop validation](/react-components-proptype-validation.md)
    1. [Components - React componenet State](/react-components-state.md)
1. [Styling React components](/react-components-styling.md)
1. [React events](react-events.md)
1. [Rendering - An introduction to React rendering](react-rendering-introduction.md)
1. [Using `state`](react-rendering-introduction.md)
1. [Resources - A list of useful articles and tutorials](react-useful-resources-articles-tutorials.md)

## Material for coming chapters

### About Class components / es6 classes

React uses default ES6 classes for their components with a couple of default methods and api's: Lifecycle hooks, API's, class properties and instance properties. You can use all these methods directly in a React class component and add your own methods as well. When you want to use a method within a method, you can call it with `this`.

In theory you can directly set the sate in a React class component like in the following example:

```javascript
class SomeComponent extends React.Component {
    state = {
        word: 'word',
    }

    render() {
        return(
            <div>{this.state.word}</div>
        )
    }
}
```

In practice you don't want to do this because it could introduce bugs. The best place to initialize your `state` is within the special `constructor()` method. The `constructor()` method is part of ES6 classes and is called on initializing of your component. In the `constructor()` method, you should also call `super()`, which somehow makes sure that `this` is available within the other methods. In any case, a React class component should look like this:

```javascript
class SomeComponent extends React.Component {
    constructor() {
        super();
        this.state = {
            word: 'word',
        }
    }

    render() {
        return(
            <div>{this.state.word}</div>
        )
    }
}
```

Notice that we now set the available `state` api with `this.state`.

### Components

Creating a stateless component: Create a function that returns a JSX element, and optionally takes in `props`.
`var MyComponent = React.createClass` and `class MyComponent extends React.Component` are sort of the same. The first one was a proprietary 'class-like' structure made by the React team while in the second example the React team switched using ES6 classes you're extending. If you want to explore the differences between the two:

- [`createClass` vs `extends React.Component` - Todd Moto](https://toddmotto.com/react-create-class-versus-component/)
- [Stackoverflow - React.createClass vs extends Component](https://stackoverflow.com/questions/33526493/react-createclass-vs-extends-component)

### Key takeaways

- JSX is a readable syntax on top of the  `React.createElement` API. The more you realise that this is the case, the better you will understand what is happening.
- The `...` spread operator is really f-in useful. The content of a JSX element is in the `children` prop so you can always define a few default values in your component and spread other values, including `children`, into them.
- Equally useful is ES6 destructuring. Destructuring props in a function call or element render is great and once you know what is going on it makes your code a lot more readable.
- In short, some ES6 knowledge is really useful when working with React.
- Extending the React class Component is nothing more than using an ES6 Class created by the React team. `React.createClass` was their own proprietary solution which they've since backed away from.
- A stateless / functional component is exactly what the name implies. Don't worry too much about which one you're using, switching them out is not that hard and the performance gain of using stateless components isn't always that high. Linters be damned.
- When you're thinking about sending data in the form of `props` or `state` from a child component to a parent component, ask yourself if it's really necessary. Most of the time you already have data available in the parent component (*needs example*)


## Lessons

### 12. [Using Class components with React](https://egghead.io/lessons/egghead-use-class-components-with-react)

The name of this lesson might be slightly misleading as it's more about event handling and binding within the React class component. I might move this part under 'event-handling'.

Working with React class components (and ES6 classes in general) means making a lot of use of `this` to reference or declare internal functions and variables. When you create a function in the *upper scope of a class component you can call that function within other functions or components by adding `this.` to the function call:

```javascript
class ClickButton extends React.Component {
    clickHandler() {
        alert('You\'ve clicked 🎉')
    }

    render() {
        return(
            <button onClick={this.clickHandler}>Click</button>
        )
    }
}
```

The example above will work with no problem. We define a function in the upper scope and refer to it within ther `render` function by adding `this.` to the function call.

But what if you want to update / set the state in the function you call in your eventhandler?

```javascript
class ClickButton extends React.Component {
    constructor() {
        super();
        this.state = {
            counter: 0,
        };
    }

    clickHandler() {
        this.setState(({ counter }) => ({
            counter: counter + 1,
        }));
    }

    render() {
        return(
            <button onClick={this.clickHandler}>{this.state.counter}</button>
        )
    }
}
```

In the example above we first call the `constructor` method. This is where we set the state. Note how we call `state` with `this.` infront of it. You could also define state on the same level as the `constructor`, withouth the `this`.

In our clickhandler, we now update the state with `setState` and add an extra count to the counter every time we click the button. It looks like it should work, but it doesn't though. The error message reads something like `Cannot read property 'setState' of undefined`.

As it turns out, `setState` is undefined because `this` doesn't refer to the upprscope anymore. We call the function within another function which means the `this` used with `setState` points to the wrong place. To fix this we can do a couple of things. One of the easiest ways to solve this is changing the `clickHandler` function to an arrow function:

```javascript
clickHandler = () => {
    this.setState(({ counter }) => ({
        counter: counter + 1,
    }));
}
```

This is the ['stage 3 public class field syntax proposal'](https://github.com/tc39/proposal-class-fields) and seems to be the recommended way of binding your function. Read more about the different possibilities in these great articles and the React Docs:

- [Why Arrow Functions and bind in React’s Render are Problematic](https://medium.freecodecamp.org/why-arrow-functions-and-bind-in-reacts-render-are-problematic-f1c08b060e36)
- [React Binding Patterns: 5 Approaches for Handling `this`](https://medium.freecodecamp.org/react-binding-patterns-5-approaches-for-handling-this-92c651b5af56)
- [Passing functions to components - React Docs](https://reactjs.org/docs/faq-functions.html#how-do-i-bind-a-function-to-a-component-instance)

*is this the right terminology?