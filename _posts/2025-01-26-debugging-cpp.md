---
layout: "post"
title: "Tackling the C++ debugging UI nightmare with Pernosco"
date: "2025-01-27 11:00:00 +1200"
permalink: "/2025/01/debgging-cpp.html"
---

There's a recent [blog post](https://core-explorer.github.io/blog/c++/debugging/2025/01/19/debugging-c++-is-a-ui.nightmare.html) about some hard problems with making interactive debuggers work well with C++. It's a very good post, go read it! In [Pernosco](https://pernos.co) we had to tackle the same problems, and in some cases were able to come up with creative solutions that I'm proud of.

## Verbose names

The full unambiguous names of C++ functions and variables include type names and namespace names, which makes them verbose. This is especially true with heavy use of templates and the type names include template parameter names (recursively). The challenge for a debugger (and other tools) is to display names that convey enough information to the user without overwhelming the UI. Pernosco tackles this by abbreviating names but making the abbreviations *interactive*.

<img src="/assets/images/2025/PernoscoNames.png" width="450" height="112" title="C++ names in the Pernosco UI">

Try it [here](https://pernos.co/debug/WV_SDEC1wkggMQGSnBsg9Q/index.html#f{m[AlDp,AA_,t[AQ,B7c_,f{e[AlDn,Jxo_,s{afw8kndAA,bARk,uCvTA9Q,oCvVwnA___/)! (Scroll down to `GenericMethod` in the "stack of selected thread".)

When we [demangle](https://docs.rs/cpp_demangle/latest/cpp_demangle/) a C++ identifier, we demangle not to a string but to a tree. Template parameters are deeper in the tree than the template, and the name of a containing scope is deeper in the tree than the item it contains. For example, consider the full name of that `GenericMethod` function:
```
mozilla::dom::BindingDetails::GenericMethod&lt;NormalThisPolicy, ThrowExceptions&gt;
```
This would be structured as:
```
(((mozilla)::dom)::BindingDetails)::GenericMethod&lt;(NormalThisPolicy, ThrowExceptions)&gt;
```
When we render the name, we replace some nodes of the tree with ellipses; clicking on an ellipsis expands that node to reveal its contents. Our default policy is to elide template parameters and to elide all but the innermost enclosing scope. This works really well.

## Compiler-generated functions

C++ programs have a lot of compiler-generated functions that aren't present in source code, such as implicit destructors, assignment operators, etc. The Coredump Explorer post highlights that this is a serious problem for interactive debuggers designed for stepping through source code.

This is much less of a problem in Pernosco because it is not designed for stepping through source code! Our [philosophy](https://pernos.co/about/vision) is that users should not step through code to build a picture of what happens over time; instead the debugger should visualize that picture for you directly. For example Pernosco's [callees view](https://pernos.co/about/callees) shows you all the function calls performed by some parent function invocation --- which works just as well whether that parent function was compiler-generated or in the source code. The callees view is also an excellent solution to the related problem that a single line of C++ code can call a huge number of functions implicitly (e.g. temporary construction/destruction and overloaded operators). In traditional debuggers you end up doing a lot of step-over calls until you finally step-into the call you care about (if you didn't accidentally step-over it, LOL RIP). With the callees view, you just scroll down to the call you care about and click on it. If you accidentally click on the wrong call, press the browser back button.

## Debuginfo problems

We do not, of course, have any magical solution to the problem that gcc and clang produce incomplete, underspecified, and often incompatible debug information. We have felt the same pain evidently behind the Coredump Explorer blog post! The underlying problem is that "gcc and gdb" and "LLVM and lldb" are mostly separate ecosystems, with the DWARF spec awkwardly trying to straddle the gap and alternative debuggers mostly invisible. We do have some small advantages; in particular Pernosco [does not](https://pernos.co/about/stacks/) need to decode call stacks.

