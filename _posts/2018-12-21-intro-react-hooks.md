---
layout: post
title: "React hooks to the rescue"
date: 2018-12-21
description: A gentle introduction to React Hooks and how they fit in the development lifecyle of your React apps.
image: /assets/images/posts/react-hooks.png
---

<p>
React Hooks are a great new feature in React 16.8. React 16.6 brought some awesome new things like <code>React.memo()</code> and React.lazy and Suspense. The React team isn't stopping there.
</p>

<p>Hooks are a feature that you'll end up using every single day of your React development. React Hooks are now out in React 16.8! Hooks are great because we get more tools as React developers.</p>

<p>
If you want to use state or lifecycle methods you would normally have to change to using React.Component and classes. Hooks let us use these features in functional components!
</p>

<hr/>
<br>

<h2>What is a hook?</h2>
<p>
Hooks are plain JavaScript functions where you can make a Hook call. They let you use state and other React features without writing a class.
</p>

```javascript
import React, { useState } from "react";

function Counter() {
  // Declare a new state variable, which we'll call "count"
  // Use array destructuring to define state a variable and
  // a function to update the state
  const [count, setCount] = useState(0);

  return (
    <div>
      <span>You clicked {count} times</span>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

<h2>Why are Hooks being proposed?</h2>
<br>
<p>
We know that components and top-down data flow help us organize a large UI into small, independent, reusable pieces. However, we often can’t break complex components down any further because the logic is stateful and can’t be extracted to a function or another component. Sometimes that’s what people mean when they say React doesn’t let them “separate concerns.”
</p>

<p>
These cases are very common and include animations, form handling, connecting to external data sources, and many other things we want to do from our components. When we try to solve these use cases with components alone, we usually end up with:
</p>

<ul>
  <li><p><b>Huge components</b> that are hard to refactor and test.</p></li>
  <li><p><b>Duplicated logic</b> between different components and lifecycle methods.</p></li>
  <li><p><b>Complex patterns</b> like render props and higher-order components.</p></li>
</ul>

<p>
The React team think that Hooks are their best shot at solving all of these problems. Hooks let us organize the logic inside a component into reusable isolated units:
</p>

<div class="flex-vid">
  <iframe height="265" style="width: 100%;" scrolling="no" title="Thinking in React (React Hooks)" src="//codepen.io/Valkendorm/embed/zMoNqM/?height=265&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/Valkendorm/pen/zMoNqM/'>Thinking in React (React Hooks)</a> by Martin Asnong
  (<a href='https://codepen.io/Valkendorm'>@Valkendorm</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>
</div>

<hr/>
<br>

<h2>Hooks doesn't introduce breaking changes</h2>
<p>
As the React team explains in the docs, developers can start gradually adopting Hooks without fear of breaking changes. This is great news for the following reasons: 
</p>

<ul>
  <li><p><b>Completely opt-in:</b> You can try Hooks in a few components without rewriting any existing code. But you don’t have to learn or use Hooks right now if you don’t want to.</p></li>
  <li><p><b>100% backwards-compatible:</b> Hooks don’t contain any breaking changes.</p></li>
  <li><p><b>Available now:</b> Hooks are now available with the release of v16.8.0.</p></li>
</ul>

<br>
<h2>Built-in Hooks</h2>
<hr/>
<br>

<h3><code>useState</code></h3>

<p>
  One of the most basics hooks you'll find, it's used to control state just like you would declare a state object in a traditional stateful component. To use it, we define: 
</p>

<code>
const [count, setCount] = useState(0);
</code>

<br>
<hr/>
<br>

<h3><code>useEffect</code></h3>
<p>
  One of the main defiencies of functional components is handling side-effects, such API calls, asynchronous operations, DOM manipulations, etc. With <code>useEffect</code>, we can operate side-effects during our component rendering, like in the snippet below:
</p>

<code>
useEffect(() => {
  document.title = `You clicked ${count} times.`
}, [count])
</code>

<br>
<hr/>
<br>

<h3><code>useContext</code></h3>

<p>
  If you want to use the Context API with functional components, Hooks has you covered. Just define a variable, and assign <code>useContext</code> with the desired context:
</p>

<code>
  const theme = useContext(ThemeContext)
</code>

<h2>Extend your hooks</h2>
<br>
<p>
  These are the most basic hooks that are explained in the React documentation. However you can use much more native hooks created by the awesome React community.
</p>

<p>You can learn more about hooks at <a href="https://reactjs.org/docs/hooks-intro.html" target="_blank">React's documentation.</a></p>
