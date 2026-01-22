---
layout: post
title: "Tinkering around with the concept of a synthetic census"
date: 2026-01-22
categories: tech
---

tl;dr Here's the [synthetic census repo](https://github.com/AkashChatterjee/synthetic-census) to directly jump into it but I've tried to highlight below how this concept came to my mind, and the future scope of this approach.

Realised recently that GitHub Actions make a lot of interesting things possible right from the phone, possibly armed with Claude Code/GitHub Copilot. Essentially, it becomes serverless compute that can run any function coded into a repository. It also has a pretty simple interface to inject variables, secrets and other config metadata directly from the convenience of your phone.

I don't do much work on the phone. The occasional Claude Code run or AWS monitoring aside, I like spending time on a larger laptop screen. However, having the option to run some powerful, bespoke analysis via the phone is compelling.

I've used deep research agents (across LLM providers and self-hosted via Ollama) for a while now. It's an excellent way to get a macro, holistic view of something before really sinking your teeth into it. But lately, when I was wondering about the power of perspectives (wrote a different [post](https://akashchatterjee.com/opinions/2026/01/19/perspectives.html) on that), I thought of tinkering with the concept of perspectives of various synthetic personas to answer queries where there's no right answer but you might want to understand all sides. These could be philosophical questions around what the definition of right vs wrong is, or even extend to major country-wide decisions like demonetization, AI policy and tax laws. A relatively under-appreciated aspect of LLMs is their ability to impersonate personas really well (given enough information and nuances about it). And I think some of the best answers I've received from LLMs was when I extensively refined the system prompt.

I've seen others use this concept of having personas in a repo to help with tasks, but it was fun playing around with this, by merging deep researched persona markdowns with questions that require demographic perspectives...all by simply running everything from the phone via GitHub Actions.

Regardless of this simple implementation, I think the concept, in general, is powerful. I can envision population-scale personas with the right % distribution coded into the sampling techniques—a synthetic census could be a great way to preempt reaction to important society-level decisions. Moreover, if the core algorithm is solid, the personas could be modular, pluggable packages, possibly hosted on S3 buckets and could lead to much richer, valuable results.