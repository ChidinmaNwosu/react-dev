---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides, markdown enabled
title: Welcome to Slidev
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply any unocss classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# https://sli.dev/guide/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true
---

# Welcome to Chidinma's Presentation

MANIPULATING THE DOM WITH REFS & SYNCHRONIZING WITH EFFECTS.

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-30">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
transition: fade-out
---


# Manipulating the DOM with refs

<h2> What is a ref?</h2>
     <p>Ref is short for reference. It allows you to store, reference and interact with DOM nodes or React components directly.</p>
     
<h2> Why do we need to use refs to manipulate the DOM?</h2>
    <p> React automatically updates the DOM to reflect changes and match the render output,
     so React components don't often need to manipulate the DOM. 
    However, sometimes you might need to access the DOM elements managed by React. For example:</p>
     <ul>
     <li v-click>Focusing an input field</li>
     <li v-click>Triggering animations</li>
      <li v-click>Scrolling to a node</li>
      <li v-click>Integrating third party libraries</li>
      <li v-click>Measure the size and position of a node</li>
     </ul>
     <p>There is no in-built way to do these things in react, so you'll have to use a Ref to manipulate the DOM node.</p>
---

# How to use Refs to manipulate the DOM.

1. Import useRef hook:
```ts {monaco}
import {useRef} from 'react';
```
2. Declare a ref; using the useRef hook declare a ref inside your component:
```ts {monaco}
const myRef = useRef(null);
```
3. Finally, pass your ref as the attribute to the JSX tag for which you want to get the DOM node:
```ts {monaco}
<div ref={myRef}>
```
4. The useRef hook return an object with a single property called CURRENT. Initially, myRef.current will be null. When react creates a DOM node for this div,react will put a reference to thsi node into the myRef.current. You can then access this DOM node from the event handlers and use the built-in browser APIs defined on it.Eg:

```ts {monaco}
myRef.current.scrollToView();
```
---

# Accessing another components Dom node

```ts {monaco}
import {useRef} from 'react'

export default function Form(){
  const inputRef = useRef(null);

  function handleClick(){
    inputRef.current.focus();
  }

  return (
    <>
    <input ref={inputRef}/>
    <button onClick={handleClick}>Focus the input</button>
    </>
  );
}

//In the example above,clicking the button will focus the input
```

---

Example 2: Incrementing a counter with a ref
```ts {monaco}
import React, {useRef} from 'react';

export default function Counter(){
  const ref = useRef(0);

  function handleClick(){
    ref.current += 1;
    alert (`You clicked ${ref.current}times!`);
  }
  return (
    <button onClick={handleClick}>Click Me!</button>
  )
}

//Here, the ref points to a number; number of counts
```
---

Disclaimer!!!

Manipulating the DOM directly is generally  frowned upon because it goes against the declarative nature of React. Refs are a powerful tool, they should be used sparingly.

By default, React doesn't let a component access the DOM nodes of other components, not even it's children. Instead components that want to expose their DOM nodes have to opt-in to that behaviour; A component can specify that it "forwards" its ref to one of its children, the you can use the fowardRef API to access it.

---

# Things to note:
When does React attache the refs?
   In React, every update is split into two(2) phases:
1. During render, react calls your component to figure out what should be on the screen.
2. During coomit, react applies changes to the DOM.

In general, you don't want to access refs during rendering. This also goes for Refs holding DOM nodes.
Because during the first render, the DOM nodes have not been created, so ref.current will be null. And during rendering of updates, the DOM nodes haven't updated yet, so it is too early. 
React sets ref.current during the commit phase. Before updating the DOM, react sets the affected ref.current to null. After updating the DOM, the React immediately sets them to the corresponding DOM nodes.

---

# Best practices for DOM manipulation with Refs.

1. Only use Refs when you have to step outside React eg managing focus, scroll positioning, calling browser API's.

2. Avoid changing DOM nodes managed by React eg modifying, adding children to, removing children from elements.

3. A component does not expose its DOM node by default.You can opt into exposing a DOM node by using a fowardRef and passing a second argument down a specific node.

---

# SYNCHRONISING WITH EFFECTS
Some components need to synchronize with external systems. For example, you might want to:
  <ul>
  <li v-click>control a non-react component based on a react state</li>
  <li v-click >set up a server connection</li>
  <li v-click>send an analytic log when a component appears on the screen</li>
  </ul>
      
Effects lets you run some code after rendering so that you can synchronise your component with some system outside of React.

---

# Key points to note:
1. What are Effects?
2. How Effects are different from events
3. How to declare an Effect in your component
4. How to skip re-running an Effect unnecessarily
5. Why Effects run twice in development and how to fix them

---

# What are Effects?
* Effects are operations that let you synchronize your React component with some external system. Eg a browser API, a third-party widget, network, etc.
* Effects lets you specify side effects caused by rendering itself, rather than an action(event). Effect is a side effect caused by rendering.
* Effects runs at the end of a commit after the screen updates.This is a good time to synchronise the React component with some external system. Eg a third-party library.

---

# Differnce between Effects and Events
   Before we delve into the differences there are two types of logic inside React components:

1. Rendering code: it lives at the top level of the component. This is where you take the props and state, transform them, and return the JSX you want to see on the screen . Rendering code must be pure; like a math formula, it should only calculate the result, but not do anything else.

2. Event handlers: they are nested functions inside your components, they perform actions rather than calculate the result. Eg Update an input field, submit a HTTP post request to buy a product, or navigate the user to another screen using a button.
Event handlers contain "side effects", they can change the programs state, through an action caused by the user eg A button click.

---

# Differences between  Effects and Event Handlers(cont.d)

* Sending a message in a chatroom is an **Event**, because it is caused by the user clicking a specific button.
* Setting up a server connection is an **Effect**, because it should happen no matter which interaction caused the component to appear.

---

# How to declare an Effect in a component.

STEP 1:
* Declare an Effect; import useEffect hook from react.
```ts {monaco}
import {useEffect} from 'react';
```
*  Then call it at the top level of your component and put some code inside yor Effect:
```ts {monaco}
function MyComponent(){
  useEffect(()=>{
    //code here will run after every render...
  })
  return ( 
    <div/>
  );
}
```
* Everytime your component renders, React will update the screen and then run the code inside useEffect.
In other worde useEffect "delays" a piece of code from running until the render is reflected on the screen.Eg:
```ts {monaco}
const [count, setCount] = useState(0);
useEffect(()=>{
  setCount(count + 1);
})
```
---

Step 2: Specify the Effect dependencies:
```ts {monaco}
useEffect(()=>{
  //code here will after every render...
}, []);
```
Note:
* It is slow: synchronizing with an external system is not always instant, so you might want to skip doing it unless it is necessary. Eg You don't want to reconnect to the chat server at every keystroke.

* It is wrong: you don't want to trigger a component fade-in animation on every keystroke, only when the component appears for the first time. You can tell React to skip unnecessary re-running the Effect by specifying an array ([]) of dependencies.
---

# Dependency Array
The dependency array ([]) can contain multiple dependencies. React will compare the dependency values using **Object.is** and will skip re-running the Effects if all of the dependencies you specify have exactly the same values as they had during previous render.

Dependencies:
```ts {monaco}
useEffect(()=>{
  //This runs after every render...
});
```
```ts {monaco}
useEffect(()=>{
  //This runs only on mount...
});
```
```ts {monaco}
useEffect(()=>{
  //This runs on mount and also if either a or b have changed since the last render...
},[a,b]);
```

---

Step 3: Add cleanup if needed
* You have a chat room component that needs to connect to the chat server when it appears.

* You are given a **createConnect() API** that returns an object  with **connect()** and **disconnect()** methods. How do you keep the component displayed to the user?

* Effect Logic:
```ts {monaco}
useEffect(()=>{
  const connection = createConnection();
  connection.connect();
});
```
* It would be slow to connect to the chat after every re-render, so add the dependency array:
```ts {monaco}
useEffect(()=>{
  const connection = createConnection();
  connection.connect();
},[]);
```
Note: The code inside the Effect doesn't use any props or state, so the dependency array is empty([]). This tells React to only run this code when the component **mounts**, ie appears on the screen for the first time.

---

```ts {monaco}
import {useEffect} from 'react';
import {createConnection} from '/chat.js';

export default function ChatRoom(){
  useEffect(()=>{
    const connection = createConnection();
    connection.connect();
  }, []);
  return <h1>Welcome to the chat room!</h1>
}
```
* In the console:
``` ts {monaco}
Welome to the chat room!

connecting...

connecting...
```
* Why is connecting... being printed twice in the console?

---

# How to handle Effects firing twice in the development?

* React intentionally remounts your components In development to find bugs, a way to fix this is to, usually implement **a cleanup function**

* Setup -> cleanup -> setup sequence

```ts {monaco}
import {useState,useEffect} from 'react';
import {createConnection} from '/chat.js';

export default function ChatRoom(){
  useEffect(()=>{
    const connection = createConnection();
    connection.connect();
    return ()=> connection.disconnect();//cleanup function...
  }, []);
  return <h1>Welcome to the chat room!</h1>
}
```
```ts {monaco}
Welcome to the chat room!
connecting...
disconnecting...
connecting...
```

---
layout: center
class: text-center
---

The End

---