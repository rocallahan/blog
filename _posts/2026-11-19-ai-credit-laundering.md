---
layout: "post"
title: "The AI Credit Laundering Problem"
date: "2026-11-19 10:00:00 +1200"
permalink: "/2026/11/ai-credit-laundering.html"
---

AI is interacting with copyright law in interesting ways, so that [most new software is probably not copyrightable](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=7682724), in the USA at least. But there is an even bigger and more general problem, which is that AI has broken the informal systems we have always used to assign credit for intellectual work.

At Google we had a situation where person _A_ developed system _X_ the hard way, then person _B_ used AI agents to develop system _Y_ to solve the same task. System _Y_ gave better results than system _X_, and included a number of techniques similar to those present in system _X_. The AI agents had access to system _X_, both directly via tool calls that retrieve code, and because system _X_ was in the training data. The question is, should person _A_ receive some of the credit for the results of system _Y_? In the pre-AI days, person _B_ would have had a good understanding of how much they had learned from system _X_ and, being a reasonable person, would have assigned credit to _A_ accordingly. Now they probably just don't know how much credit to assign. Person _A_ will justifiably feel aggrieved.

The problem is even bigger than this. In the real case I'm thinking of, _A_ and _B_ were known to each other, but in general they might not be; person _B_ might never know system _X_ existed. In the limit, it seems fair to say that _everyone who contributed to the training data or whose contributions might have been accessed by the AI agent_ should be eligible for credit.

This is a severe problem. It has come up forcefully in mathematics, where AI-powered proofs seem inspired by specific results from specific human mathematicians but we don't know for sure.

It may be possible to analyze transcripts, tool call logs and (if available) reasoning tokens to do some credit assignment. That would be a pretty interesting problem to work on! But it certainly wouldn't capture learning from pretraining, so it won't solve the problem.

We certainly need to acknowledge the problem with humility: in general we don't know where credit should go.

I think we probably need to take the expansive view above, which generalises the "AI-generated code is not copyrightable" position: when you use AI to solve a problem, most of the credit for that solution diffuses out to everyone who contributed to the training data and other accessible resources. When AI proves mathematical theorems or generates code, most credit should not go to the people commanding the AI, nor the AI itself, but to the large fraction of humanity (living and dead) on whose collective works the AI was grown.

This also suggests an economic fairness principle: because it's so much harder to assign credit to specific antecdents of AI-generated work than for human-generated work, we should tax AI work for the benefit of the public more than we tax human work. And of course, in general, we should not allow companies to distill the public intellectual commons into their models and capture all the profits for themselves.

