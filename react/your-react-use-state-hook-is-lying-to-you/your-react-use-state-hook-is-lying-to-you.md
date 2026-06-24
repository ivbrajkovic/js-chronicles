<p align="center">
  <img src="./images/wintage-conters-compressed.png" width="800" />
    <p align="center" style="font-size: 0.8em; color: gray; margin-top: -10px;">
  </p>
</p>

# Your React `useState` Hook Is Lying to You

##### _A journey into the dark magic of state persistence, closure tricks, and why React judges you by the order of your hooks._

### Table of Contents

- [The Day My State Disappeared](#the-day-my-state-disappeared)
- [Local Variables Are Liars](#local-variables-are-liars)
- [We Need a Render Button](#we-need-a-render-button)
- [The Closure That Saved My Sanity](#the-closure-that-saved-my-sanity)
- [One State Is Never Enough](#one-state-is-never-enough)
- [The Index Is Everything](#the-index-is-everything)
- [Why Order Matters (And React Will Judge You)](#why-order-matters-and-react-will-judge-you)
- [The Reset Button Nobody Talks About](#the-reset-button-nobody-talks-about)
- [What We Actually Built](#what-we-actually-built)
- [The Part Where We Acknowledge Reality](#the-part-where-we-acknowledge-reality)

---

## The Day My State Disappeared

I was three hours into debugging a form component. The state kept resetting. Not to the initial value—that would have been too sensible. No, it was resetting to _undefined_ on every render. The error message was unhelpful. The stack trace was a maze. My coffee was cold, and I was questioning every life choice that led me to become a React developer.

Then it hit me: I had no idea how `useState` actually worked.

I'd been using it for years. I knew the rules—don't call hooks conditionally, don't call them inside loops, don't call them after a `return`. I followed them like a good developer. But I didn't understand _why_.

So I did what any reasonable developer does: I built my own.

Not because I wanted to replace React. Because I needed to understand the magic. And let me tell you—once you see how the trick works, you stop being impressed and start being terrified.

---

## Local Variables Are Liars

Let's start with the most obvious question: why can't we just use a regular variable?

```tsx
const Counter = () => {
  let count = 0;

  const increment = () => {
    count++;
    // Something should update here, right?
  };

  return <button onClick={increment}>{count}</button>;
};
```

This doesn't work. You know this. I know this. But _why_?

Every time `Counter` runs, it creates a fresh `count` variable. The function executes, returns some JSX, and then... it's done. The `count` variable dies. Garbage collected. Gone forever.

When you click the button, `increment` runs, changes `count`, and then... nothing happens. Because React has no idea you changed a local variable. It already finished rendering. The DOM still shows `0`. There's no second render.

**React only re-renders when something tells it to.** Local variables don't send memos.

So we need state that persists across renders. But here's the kicker: it also needs to be accessible inside the component function, every time it runs. And it needs to trigger a re‑render when it changes.

That's a lot of requirements for a humble variable.

---

## We Need a Render Button

Before we can fix state, we need a way to re‑render the UI. In a real React app, this is handled by the framework. But for our mental model, let's create a tiny wrapper around React's own rendering mechanism.

```tsx
import React from 'react';
import ReactDOM from 'react-dom';

const render = () => {
  ReactDOM.render(<App />, document.getElementById('root'));
};
```

That's it. We're not building a virtual DOM or a reconciliation engine. We're just using React's battle‑tested renderer so we can focus entirely on **state**. Whenever we want to update the screen, we call `render()` and let React do the heavy lifting.

Later, we'll add one crucial line to this `render` function—but for now, it's our re‑render trigger.

---

## The Closure That Saved My Sanity

Let's solve the first problem: persistence.

```tsx
let outsideCount = 0;

const Counter = () => {
  const increment = () => {
    outsideCount++;
    render(); // Trigger a re‑render
  };

  return <button onClick={increment}>{outsideCount}</button>;
};
```

`outsideCount` lives outside the component function. It survives between renders. When `increment` runs, it updates the variable and calls `render()`. The new render sees the updated value.

But we have a problem: `outsideCount` is shared. Every component that uses it sees the same value. That's not state. That's a global catastrophe waiting to happen.

We need separate state for each component. And we need separate state for _each piece of state_ within a component.

But we also need something else: the `increment` function needs to know which `count` it's updating. This is where closures come in.

When you call `useState(0)`, you get back a `setValue` function. That function has a secret—it knows exactly which state slot it's supposed to update.

```tsx
const useState = (initialValue) => {
  const index = someUniqueIndex; // Where does this come from?

  const setValue = (newValue) => {
    state[index] = newValue;
    render();
  };

  return [state[index], setValue];
};
```

The `setValue` function closes over `index`. It remembers which state slot belongs to it. When you call `setValue(42)`, it updates the right spot in the state array, even though it's being called from a completely different render cycle.

**This is closure magic.** The `setValue` function carries its `index` with it like a backpack. No matter when or where it's called, it knows exactly where to write.

---

## One State Is Never Enough

Now we have persistence. We have per‑component state. But we're still using a single value.

```tsx
let currentState = 0;

const useState = (initialValue) => {
  if (currentState === undefined) currentState = initialValue;

  const setValue = (newValue) => {
    currentState = newValue;
    render();
  };

  return [currentState, setValue];
};
```

This works for exactly one piece of state. Try using it twice:

```tsx
const App = () => {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('Alice');
  // Both use the same `currentState` variable
};
```

The second call overwrites the first. Count is lost forever.

We need an array. Each `useState` call gets its own slot.

```tsx
let stateIndex = 0;
const state = [];

const useState = (initialValue) => {
  const index = stateIndex;

  if (state[index] === undefined) state[index] = initialValue;

  const setValue = (newValue) => {
    state[index] = newValue;
    render();
  };

  stateIndex++;

  return [state[index], setValue];
};
```

Now each hook call gets a unique index. The first call gets index `0`, the second gets index `1`, and so on. The state persists in the array, and each `setValue` closes over its specific index.

This works beautifully. Until you re‑render.

---

## The Index Is Everything

On the first render, the component runs and collects its hooks in order:

```tsx
// First render
const [count, setCount] = useState(0); // index 0
const [name, setName] = useState('Alice'); // index 1
```

`stateIndex` is `2` when the render finishes. The state array holds `[0, 'Alice']`.

Then the user clicks something. `setCount` updates `state[0]` to `1`. It calls `render()`. The component runs again.

Here's the critical part: when the component runs again, `stateIndex` must be reset to `0`. Otherwise, the next `useState` call would get index `2`, leaving gaps in the array.

So let's enhance our `render` function:

```tsx
const render = () => {
  ReactDOM.render(<App />, document.getElementById('root'));
  stateIndex = 0; // Reset for the next render
};
```

But wait—if `stateIndex` is always `0` at the start of a render, how does the component know which state belongs to which hook? It doesn't. It relies on _call order_.

---

## Why Order Matters (And React Will Judge You)

React's rule is simple: hooks must be called in the exact same order on every render.

Here's why, with our implementation:

```tsx
// Render 1
const [count, setCount] = useState(0); // index 0
const [name, setName] = useState('Alice'); // index 1

// Render 2 (same order)
const [count, setCount] = useState(0); // index 0
const [name, setName] = useState('Alice'); // index 1
```

`count` always gets index `0`. `name` always gets index `1`. Everything works.

Now let's break it:

```tsx
// Render 1
const [count, setCount] = useState(0); // index 0
const [name, setName] = useState('Alice'); // index 1

// Render 2 (different order)
const [name, setName] = useState('Alice'); // index 0
const [count, setCount] = useState(0); // index 1
```

Now `name` is reading from index `0`, which contains the count value. `count` reads from index `1`, which contains the name. The values are swapped. Chaos ensues.

This is why hooks can't be conditional. This is why they can't be inside loops (unless the loop runs the same number of times every render, which is practically impossible). This is why React enforces the Rules of Hooks with an iron fist.

**React uses the order of hook calls as identity.** Each hook is identified by its position in the sequence, not by its name or purpose.

---

## The Reset Button Nobody Talks About

We reset `stateIndex` after every render. This seems counterintuitive—shouldn't we keep incrementing? But resetting is essential.

```tsx
const render = () => {
  ReactDOM.render(<App />, document.getElementById('root'));
  stateIndex = 0;
};
```

If we didn't reset, `stateIndex` would keep growing. On the first render, it ends at `2`. On the second render, it starts at `2` and ends at `4`. The state array would have gaps—empty slots that previous renders filled. The component would never access its own state again.

Resetting ensures that each render starts from scratch. The hooks are always assigned in the same order, always finding their state in the same slots.

It's like resetting a pointer to the beginning of an array before reading it. You always start at zero, always read sequentially, always get the data that belongs to you.

---

## What We Actually Built

Let's look at the whole picture:

```tsx
let stateIndex = 0;
const state = [];

const useState = (initialValue) => {
  const index = stateIndex;

  if (state[index] === undefined) state[index] = initialValue;

  const setValue = (newValue) => {
    state[index] = newValue;
    render();
  };

  stateIndex++;

  return [state[index], setValue];
};

const render = () => {
  ReactDOM.render(<App />, document.getElementById('root'));
  stateIndex = 0;
};
```

This is not how React actually works. But it captures the essential ideas:

1. **State lives outside the component**, in an array or similar data structure.
2. **Each hook call gets an index** based on its order in the component.
3. **`setValue` closes over its index**, so it always updates the right state.
4. **The hook index resets on every render**, ensuring consistent mapping.
5. **Call order is identity**—change the order, change the behavior.

The real React is vastly more complex. It uses fibers, update queues, batching, concurrent rendering, and a whole host of optimizations. But the core idea—state stored externally, accessed by index, updated through closures—is the same.

---

## The Part Where We Acknowledge Reality

React's actual `useState` is a monster compared to our little creation. It deals with:

- **Fibers**: React's internal tree structure that holds state for each component.
- **Update queues**: State updates are batched and processed asynchronously.
- **Concurrent rendering**: React can pause and resume rendering, which requires even more careful state management.
- **Batching**: Multiple `setState` calls can be merged into a single update.
- **Lazy initialization**: Passing a function to `useState` that runs only on the first render.
- **Suspense and error boundaries**: State needs to survive rendering interruptions.

Our implementation doesn't do any of that. It's a toy. A beautiful, educational toy that explains why the rules exist.

But that's the point. Once you understand how the index works, you understand why you can't call hooks conditionally. Once you understand the closure, you understand why `setValue` always works even after many renders. Once you understand the array, you understand why state persists.

**The rules aren't arbitrary. They're logical consequences of the design.**

So next time you're staring at a stale closure or a hook order error, you'll know what's happening inside. You'll know that React isn't being picky. It's just trying to match your state to the right hook, in the right order, at the right time.

And maybe, just maybe, you'll feel a little less frustrated. Because now you know the trick.

---

_This article was inspired by the countless hours I spent staring at React source code and wondering why it works the way it does. The toy implementation is a simplified version of the mental model that finally made it all click._

_All code examples are fictional and for educational purposes. If your production app uses a global `state` array, we need to talk._
