# React Conf 2018 - Thursday October 25, 2018

## React Today and Tomorrow - [Sophie Alpert](https://twitter.com/sophiebits) & [Dan Abramov](https://twitter.com/dan_abramov)

#### [Sophie Alpert](https://twitter.com/sophiebits)

1.25 Million developers are using the react dev tools. It's growing like crazy. React just passed jQuery in google search trends.

React's mission is to make it easier to build great UIs. How they do this/focus areas:

1.  Simplify things that are hard
1.  Performance
  - Try and make react faster so we don't have to spend as much time optimizing
  - They're working on time-slicing rendering
1.  Developer tooling

#### What in React still sucks?

1. Reusing Logic
  - Higher-order components are gnarly
  - Component hierarchies can become insane and deep
1. Giant Components
  - It's really easy to make huge, gnarly components. React can make it easy to do that.
1. Confusing classes
  - JS classes are janky and weird.  'Cause JS.
  - Function components aren't easily turned into class components
  - `this` binding is always confusing
  - Classes are both hard for humans and hard for computers.  It makes it hard to implement hot reloading reliably.

#### [Dan Abramov](https://twitter.com/dan_abramov)

How do we fix those problems?  If you change one thing to make something better, it makes one of the other problems worse.

The real problem is that React doesn't provide a _stateteful primitive_ simpler than a _class component_.

React used to have mixins. You could share methods across classes fairly easily that way.  The problems that mixins create are worse than the problems that they solve.

They're introducing an opt-in, all net-new, so no breaking changes API

They're adding an API called `useState`.  Lets you use the notion of state in a function component so you don't get the overhead of a class component.

```js
import React, { useState } from 'react';

export default function ComponentName(props) {
  const [prop, setProp] = useState('initial state');
}
```

State can be any primitive with the `useState` api.  Can be any primitive.

Calling the `setProp` function triggers a re-render just like `this.setState` in a full class component.

Works with the context api.  You can get concepts of that in a function component with a much smaller/simpler api now.

The order of the `useState` calls is going to matter.  You can't call them inside of some check for condition of props.  You have to just declare them at the top of the file.

There is also a new api called `useEffect` that is essentially shorthand for

```js
import { useEffect } from 'react'

useEffect(() => {
  // do something...
})
```

What ever you do in the `// do something...` will be done in the `componentDidMount` and `componentDidUpdate`

If you return a function from `useEffect`, it will call that function to 'clean up' after the component unmounts.  Basically shorthand for `componentWillUnmount`

You can create custom hooks.  They always have to start with `use..`, ie `useWindowWidth`.  You can then use your custom hook to manage state just like the `useState` api

You get A LOT more power out of your function components while making them MUCH cleaner, and easier to read.  You end up cutting out a lot of the wrapper 'hell' that its really easy to get into

Check out the docs [here](https://reactjs.org/hooks)

DON'T REWRITE YOUR COMPONENTS/APPS IN HOOKS.  It's still just a proposal / in alpha at this point.  Try and play with them, but DON'T re-write everything.

## 90% Cleaner React - [Ryan Florence](https://twitter.com/ryanflorence)

Ryan is going through demos of refactoring existing react components to use the new hooks.  Super interesting.  It really is 90% cleaner!

## Building Todo The Game In A Cloud-Only Dev Environment - [Christina Holland](https://twitter.com/americanwombat)

A demo of how feasible it is to dev on a chrome book.  Moving all your dev environment stuff to AWS Cloud9.

There are a few circumstances where this approach can be useful.  Not having your computer, having a crappy computer, teaching a class, etc.  Its useful in some contexts to have a cloud dev environment

This is a really crappy setup if you don't have reliable internet, have a local dev environment, are picky about your dev environment.

A pretty neat demo!

## The path to a declaratively animated future - [Matt Perry](https://twitter.com/popmotionjs)

Animations have traditionally been SUPER imperative.  Using a [library called POSE](https://popmotion.io/pose/) to try and make things feel more react-y and more declarative.

Animations get a bad rap.  They get left for last, and are subservient to "does it work".

CSS itself is already a pretty powerful declarative animation library.  However there are a handful things it doesn't do.  We need something that combines the simplicity of CSS syntax with the power of the things you can do in JavaScript.

On the js side, you can do stuff like this:

```js
// You write posed components with styled components-like syntax
const Button = posed.button({
  hoverable: true,
  initial: { scale: 1 },
  hover: { scale: 1.2 }
});

// And then use them like normal
<Button />
```

Pose has some pretty strong opinions about the orders of properties.  Transform an object before you translate it, for example.

Also, it abstracts some of the animation away from you and automates it.  

## ⚡️ Talks

#### What's new in create-react-app V2
- PostCSS
- Sass and CSS Modules support out of the box!
- Aaaand... a bunch of other stuff on the create react app blog
- TypeScript support is coming TODAY!  Available as a cli option!

#### Components as units of work
- You can use components as more than just containers for presentation logic
- They can be containers for abstracting other functionality too!

#### 5 Animations You Should Know and Use
- Hover - Makes something seem clickable.  Can make things seem important too.
- Sticky (`position: fixed`).  Not sure this is an animation, but love it!
- Loading animations
- Sliding - translate x and y
- Opacity - fade in / out

#### Render Props + Pure Components maybe = problems??? (Not his actual title)
- Dude moved FAST through his talk.  And it was a little tough for me to follow, honestly.  The post-lunch tired is starting to kick in.

#### Better Living Through Git Hooks
- The `.git` directory has a lot of cool things you didn't know about in it!
- The `.git/hooks/` directory can be super useful for keeping things clean
- She owns [thatpersonfromthething.com](https://thatpersonfromthething.com) and it redirects to her personal site.

## GraphQL without GraphQL - Backend Agnostic GraphQL inspired Queries For Colocating Data Requests With Components - [Conor Hastings](https://twitter.com/stillconor)

TODO

## Playing With Polyhedra: Creating Beauty from Obsession - [Nat Alison](https://twitter.com/tesseralis)

TODO

## Developing Immersive cross-platform AR and VR Apps using React-Native - [Pulkit Kakkar](https://twitter.com/pulkitkkr0)

TODO

## Beyond web-apps: React, JavaScript and WebAssembly to port legacy native apps - [Florian Rival](https://twitter.com/FlorianRival)

TODO

## React for social change - [Rodrigo Quezada](https://twitter.com/rodikana)

TODO
