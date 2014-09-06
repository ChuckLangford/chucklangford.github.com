---
layout: post
title: The Critical Rendering Path
---

Not too long ago, my development studies took me down the web performance path.  There's a lot to cover when it comes to web site performance ranging from highly technical to just plain common sense.  In this post, I'm going to focus on what I see as a peformance fundamental once you've moved past the [common sense development](https://developer.yahoo.com/performance/rules.html).  Let's talk about the Critical Rendering Path.

### What is the Critical Rendering Path?

Most of the time, a developer is concerned with the performance of his code either on the server or in the browser.  We're mindful of the objects we create, the memory we consume and how quickly our code executes.  This is great stuff, but there's another opportunity for improvement.  How quickly can the browser fetch, parse and render our website?  How long does it take for something to appear on the screen after the user clicks a link?  This is what the Critical Rendering Path is all about.

To be thorough, here's a definition directly [from Google](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/):

> Optimizing for performance is all about understanding what happens in these intermediate steps between receiving the HTML, CSS, and JavaScript bytes and the required processing to turn them into rendered pixels - that’s the critical rendering path.

### The CRP at 30,000ft

The link above is great reading and tells you everthing you want to know about the CRP, so I'm not going to repeat them here.  Instead, here's my "CRP Elevator Talk".

In order to display our website, the browser has to go through a few steps:

1.  Download all the necessary resources.
2.  Parse and create the DOM.
3.  Parse and create the CSSOM.
4.  Combine the DOM and CSSOM into a Render Tree
5.  Execute Javascript.
6.  Render the page.

### What is a Blocking Operation?

A blocking operation is anything that keeps the browser from 

### Can I see the CRP in Action?

### CRP Applied