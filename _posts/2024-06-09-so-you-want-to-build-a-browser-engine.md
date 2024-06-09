---
layout: "post"
title: "So You Want To Build A Browser Engine"
date: "2024-06-09 11:00:00 +1200"
permalink: "/2024/06/browser-engine.html"
---

If you're building a browser engine from scratch just for fun, stop reading now and I wish you the best. If you want to build an engine that's competitive with Chromium, and surpasses it in some respects, here's my advice.

## Security and performance requirements tightly constrain your design

When designing your engine, focusing from the beginning on the right set of important and hard use-cases will help you a lot: much of your design will be dictated by the performance and security requirements of those use-cases. Here some use-cases I think are very constraining, and how I think they constrain browser engine designs in interesting ways.

### Site isolation

In the post-Spectre world you must have _site isolation_. The JS for a site (roughly, eTLD+1) must have its own OS address space separate from other sites. (This means IFRAMEs must work across process boundaries.)

A key part of engine design is determining exactly what is in these sandboxed content processes and how they interface with the rest of the browser. To minimize RAM usage, and the risks of exposing data to site JS via Spectre or engine bugs, you want to put the bare minimum in these processes, starting with JS application state. JS needs very fast access to the DOM state, so the page DOM needs to be in the content process. CSS layout is also tightly coupled to the DOM and JS so you'll have your layout data structures and logic in there too. Beyond that, it's less clear, but you don't want image data in your content processes for example.

The design of your JS engine and DOM and layout implementation will be partly driven by the need to keep RAM usage per content process to a minimum. Any caches that could be useful across sites should be shared between content processes whenever you can do that safely, e.g. supply font metrics via read-only shared memory from some other process.

### Content main thread sanctity

Normal page JS runs on a single "main thread", which is very often the bottleneck for application execution. So don't block those threads for other work unless you absolutely have to because it can't run concurrently with page JS. Will you be running layout on those threads? Rendering?

Since page JS often runs slowly, for responsiveness you want to avoid browser features blocking on those main threads as much as possible.

Because they're so critical, modern browser engines have sophisticated heuristics for scheduling the activities of these main threads. Yours probably will too.

### Fast JS-DOM calls (WebIDL)

Many applications depend on making lots of DOM API calls, so calling DOM APIs from JS must be as efficient as possible. You'll be counting instructions and cycles. You'll want to share strings across interface boundaries, etc. Get your JS JIT involved.

On the other hand, you'll be implementing a huge number of WebIDL interfaces so make that as ergonomic for developers as possible.

You'll need to GC cycles spanning DOM/JS boundaries. This heavily constrains DOM memory management and JS GC.

### Page load performance

The latency from starting to load a URL to getting content on screen must be absolutely minimized. This means you need to do a lot of things well.

You need prerendering --- when it looks like the user is going to load a URL but hasn't commited, be able to start loading and rendering early.

You need to implement modern HTTP with aggressive caching, bandwidth management, prioritization etc.

Your HTML parser must be incremental so you can get content on the screen before you have loaded the entire document. It must support prefetching --- as soon as you see the URL of a subresource, start fetching it. As noted above, it can't block the site JS main threads. (Although you will need to be able to invoke it synchronously for innerHTML.) It's a similar story for parsing other kinds of resources --- CSS, JS, images. At what point is loaded content injected into your content processes?

Having built the DOM, you need crazy fast CSS style resolution and layout. This is a huge topic because DOMs are so varied. You will need very fast text shaping (probably with shaped-word caching). Maybe you want incremental layout.

Then you need really fast rendering of laid-out DOM into whatever your compositor (see below) needs. You will probably need to use the GPU to be competitive, so maybe this is split into a CPU side and a GPU side. At what point does the output leave the content process?

### Scrolling and animations

Scrolling and animations must not stutter just because page JS is running. So, you need to make sure you can render scrolling and animation updates without blocking on the page main threads. Typically this is done by building a scene graph that a "compositor" can render without blocking on those threads. The compositor will need a way to process scrolling input events without blocking on DOM event dispatch in JS main threads. For smooth animations the compositor needs to be aware of vsync, and this has to be plumbed through to content process DOM APIs like requestAnimationFrame; your vsync architecture will be a story in itself.

Your compositor will need to support the full set of SVG and CSS filters, plus cropping, masking and other effects, across frame boundaries.

### Typing latency

You must minimize the latency of responding to input with screen output. A good proxy for this is latency of typing into a textarea or, say, Google Docs. Study your pipeline for receiving a system keyboard event, dispatching a DOM event in the content process, laying out the updated DOM, rendering and compositing the result to the screen and make sure it's minimal. See vsync above!

### Quality video playback

High-quality video streaming is hard and constrains your audio and graphical output systems. Obviously you need to implement the basic DOM APIs and relevant extensions such as EME and MSE. You'll want to sandbox container demuxing in its own process. You will need to integrate modern codecs. You will need to support hardware decoding when that's available. For A/V sync you need to present a queue of decoded frames to your compositor, which will sync on the audio output clock. You need to minimize power usage by integrating with the system compositor where that's possible. Obviously this has to be zero-copy for decoded frames.

## Going beyond

If you've done all that and implemented all the Web specs, you might still only be a less-Web-compatible Firefox or Chromium. What can you do better? My knowledge is a bit out of date, but here are a few guesses.

Go parallel from the ground up. You'll get more and more E-cores, so you should try to use them. Parallel parsing and layout seem like endless opportunity.

Use a programming language that lets you write clean, fast, memory-safe, parallel data-race-free code --- probably Rust.

It's annoying how current browsers lose state when they restart for updates etc. In principle you can serialize content process state, restart the browser, and restore the full page state. This probably has other uses.

You are going to spend an infinite amount of time diagnosing your engine's bugs on inscrutable Web sites. Build really incredible tools for that and maybe Web developers will like them too. Consider replay.io, AI, and whatever else you can think of.

Good luck! You deserve it, and you'll need it!

