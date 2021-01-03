---
layout: post
title: "Enter React Server Components"
date: 2020-12-25
description: An introduction to what React server components are and how this broadens React use cases.
image: /assets/images/posts/react-server-components.png
---

Recently, Dan and Lauren from the React team [made a talk](https://www.youtube.com/watch?v=TQQPAU21ZUw) introducing a experimental feature from React called zero-bundle-size Server Components.

It's a new (old) way to render React components but from a server-driven perspective.It's not exactly SSR of components, and this can pontentially result in much smaller bundle-sizes.

Although it's not quite ready for production yet, it's worth to take a look at how it works and how it might be a good fit for certain use cases.

## Current SSR has limitations

Today's Server-side rendering of client-side JavaScript can be suboptimal. JavaScript for your components is rendered on the server into an HTML string. This HTML is delivered to the browser, which can appear to result in a fast First Contentful Paint or Largest Contentful Paint.

However, JavaScript still needs to be fetched for interactivity which is often achieved via a hydration step.

Server-side rendering is generally used for the initial page load, so post-hydration you're unlikely to see it used again.

Now with Server Components, components can be refetched as they're required. This limits the amount of code that needs to be send to the client.

```javascript
// MarkdownPreview.js: *before* Server Components

import marked from 'marked'; // 35.9K (11.2K gzipped)
import sanitizeHtml from 'sanitize-html'; // 206K (63.3K gzipped)

function MarkdownPreview({text}) {
  const html = sanitizeHtml(marked(text));
  return (/* render */);
}
```

## Enter Server Components

React's new Server Components compliment Server-side rendering, enabling rendering into an intermediate abstraction format without needing to add to the JavaScript bundle. This both allows merging the server-tree with the client-side tree without a loss of state and enables scaling up to more components.

Server Components are not a replacement for SSR. When paired together, they support quickly rendering in an intermediate format, then having Server-side rendering infrastructure rendering this into HTML enabling early paints to still be fast. We SSR the Client components which the Server components emit, similar to how SSR is used with other data-fetching mechanisms.

This time however, the JavaScript bundle will be significantly smaller. Early explorations have shown that bundle size wins could be significant (-18-29%), but the React team will have a clearer idea of wins in the wild once further infrastructure work is complete.

```javascript
// MarkdownPreview.server.js - Server Component === zero bundle size

import marked from "marked"; // zero bundle size
import sanitizeHtml from "sanitize-html"; // zero bundle size

function MarkdownPreview({ text }) {
  // same as before
}
```

## Built-in code-splitting

It's been considered a best-practice to only serve code users need as they need it by using code-splitting. This allows you to break your app down into smaller bundles requiring less code to be sent to the client. Prior to Server Components, one would manually use React.lazy() to define "split-points" or rely on a heuristic set by a meta-framework, such as routes/pages to create new chunks.

```jsx
// Chart.js (before Server Components)
import React from "react";

// one of these will start loading * only when rendered on the client*:
const OldChartRenderer = React.lazy(() => import("./OldChartRenderer.js"));
const NewChartRenderer = React.lazy(() => import("./NewChartRenderer.js"));

function Chart(props) {
  if (FeatureFlags.useNewChartRenderer) {
    return <NewChartRenderer {...props} />;
  } else {
    return <OldChartRenderer {...props} />;
  }
}
```

Some of the challenges with code-splitting are:

- Outside of a meta-framework (like Next.js), you often have to tackle this optimization manually, replacing import statements with dynamic imports.

- It might delay when the application begins loading the component impacting the user-experience.

Server Components introduce automatic code-splitting treating all normal imports in Client components as possible code-split points. They also allow developers to select which component to use much earlier (on the server), allowing the client to fetch it earlier in the rendering process.

## Do Server components make Next.js SSR obselete?

Short answer is no. They are quite different. Initial adoption of Server Components will actually be experimented with via meta-frameworks such as Next.js as research and experimentation continue.

To summarize a good explanation of the differences between Next.js SSR and Server Components from Dan Abramov:

- Code for Server Components is never delivered to the client. In many implementations of SSR using React, component code gets sent to the client via JavaScript bundles anyway. This can delay interactivity.

- Server components enable access to the back-end from anywhere in the tree. When using Next.js, you're used to accessing the back-end via getServerProps() which has the limitation of only working at the top-level page. Random npm components are unable to do this.

- Server Components may be refetched while maintaining Client-side state inside of the tree. This is because the main transport mechanism is much richer than just HTML, allowing the refetching of a server-rendered part (e.g such as a search result list) without blowing away state inside (e.g search input text, focus, text selection)

## Conclusion

What you should take away from Server Components:

In a typical app, tons of components only rerender when there’s new data. This will let you run those components on the server instead.

Thus, **reducing bundle size by a lot.**

## Relevant reading

To learn more about this work, watch the talk from Dan and Lauren, read the RFC and do check out the Server Components demo to play around with this work. With thanks to Sebastian Markbåge, Lauren Tan, Joseph Savona and Dan Abramov for their work on Server Components.

- [Lauren and Dan's talk](https://twitter.com/sugarpirate_/status/1341141198258524163)

- [Addy Osmani blog post](https://addyosmani.com/blog/react-server-components/)

- [Server components demo](https://github.com/reactjs/server-components-demo)

- [Official React blog post](https://reactjs.org/blog/2020/12/21/data-fetching-with-react-server-components.html)
