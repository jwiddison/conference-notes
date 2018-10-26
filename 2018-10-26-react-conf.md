# React Conf 2018 - Friday October 26, 2018

## Concurrent Rendering in React - [Andrew Clark](https://twitter.com/acdlite) & [Brian Vaughn](https://twitter.com/brian_d_vaughn)

### [Andrew Clark](https://twitter.com/acdlite)

Hooks are good because they let us spend time thinking about code, and more time thinking about user experience.

Performance is an important part of UX design. If an app is slow, it'll always be a bad experience.

Code splitting can be helpful ,but what do you display to the user if the view that they want to look at hasn't finished loading.

So they're introducing something called _Concurrent React_

Today react is synchronous. This project was called asynchronous react at one point. It's a way of time-slicing. And its non-blocking so you get a better user experience.

#### `lazy()`

New API for code-splitting a single component.

```js
import React, { lazy } from 'react';
const Chart = lazy(() => import('./Chart'))
// So you can 'lazily' import a component.  Then all you have to do is provide a fallback component

// React will look for a 'suspense component'
import React, { lazy, Suspense } from 'react';

// Then you can wrap compoments you want to lazy load in <Suspense />
<Suspense fallback={<Spinner />} >
  {...} // Your content
</Suspense>

// Where <Spinner /> is some loading animation or some component to render in place of the component that hasn't rendered yet
```

This makes it work in normal, synchronous react. You can run things in concurrent mode and not show the `<Spinner />` if things load within a certain threshold.

## Moving to Suspense - [Jared Palmer](https://twitter.com/jaredpalmer)

You can use suspense for lazy loading asynchronously. You can pretty easily do things like loading a lower resolution version of an image until the high-res version is loaded.

There's a lot to wire up so I'm not putting the code examples in here. But its a super cool idea!

You can do it with fonts, images, audio, video, etc. You can also do it with Data, but that is still pretty expirimental for now. And if you're going to be using something like Apollo that'll already be abstracted away in Apollo.

It enables you to delete all the concepts of `isLoading` state that everyone uses all over the place. Even if you don't use the concurrentReact packages, you can still use Suspense and use smarter fallback components to render while larger/data-intensive things are loading.

## SVG Illustrations as React Components - [Elizabet Oliveira](https://twitter.com/miuki_miu)

To import an SVG as a react component, this is one way to do it:

```js
import { ReactComponent as Logo } from "../path/to/svg/file.svg";

const LogoComponent = () => {
  return <Logo />;
};
// This is a new feature in create react app.  You don't have to convert the SVG to JSX.
```

BUT if you do convert to JSX, you can do code-splitting, make use of props, state, event handlers.

A good example is her React Kawii project. [Here's the GitHub](https://github.com/miukimiu/react-kawaii) and [Here's the docs](https://react-kawaii.now.sh/#/Components?id=icecream). A good showcase of how you can use props and stuff to customize SVGs and make really useful visual components.

## The Missing Abstraction of Charting - [Chris Trevino](https://twitter.com/darthtrevino)

Chris works for Microsoft doing data stuff. He explained it but I dunno.

Most of the charting libraries we end up using are pretty brittle. The options that you currently have are pretty crappy. Either a pretty primitive charting library that won't meet your requirements or building something from scratch. So what other options are there?

There are some good libraries at this point that are popular with React. React-vis and stuff like that.

Vega - a way of making data visualizations using JSON data. You configure and set it up with a bunch of JSON objects. Kinda like D3 so far. Instead of being a component-based charting library like most of the popular React charting library.

Chris is making a library called Chart Parts. Still super experimental, so I can't link docs or anything in here. It's cool because it has a lot of support for visual parity on mobile devices.

The rest of the talk was a walkthrough of how you're gonna be able to use his library.

## Elsa: AI Conversational Agent Aimed At Improving Mental Health For Women - [Damini Satya Kammakomati](https://twitter.com/Daminisatya)

Damini was introduced as being "the last speaker before lunch". Most of the other people get really specific introductions.

Elsa is trying to be a stand-in for the friends that people typically try to reach out to when they're struggling with mental health problems.

Most of the existing mental health chat bots and stuff range from not-very-effective to actually counter-productive.

## Block in the Main Thread - [James Long](https://twitter.com/jlongster)

## React Native's New Architecture - [Parashuram N](https://twitter.com/nparashuram)

## Let React speak your language - [Tomáš Ehrlich](https://twitter.com/tomas_ehrlich)

## React for Designers with FramerX - [Thomas Aylott](https://twitter.com/subtlegradient)

## Building a diverse and inclusive community - [Eyitayo Alimi](https://twitter.com/alimieyitayo)
