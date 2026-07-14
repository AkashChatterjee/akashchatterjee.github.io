---
layout: post
title: "Cognitive disengagement is becoming an epidemic"
date: 2026-01-27
categories: life
redirect_from:
  - /opinions/2026/01/27/cognitive-disengagement-epidemic/
description: "How over-reliance on AI coding assistants like ChatGPT and GitHub Copilot may harm cognitive development in software engineers. Research-backed insights."
---

Recently I was reading about neural pathways and going down a rabbit hole of research around techniques that correlate with cognitive growth. I was trying to figure out what strategies would most likely be successful to train a new generation of software engineers as they enter a world where top leaders are constantly talking about the need to "master AI tools" and declaring that "coding is dead". It's almost as if the next generation is being encouraged to increasingly focus on using the modern AI/LLM apps like ChatGPT, Gemini and Claude and make it an integral part of their learning process.

I have a different take on this. Here are my main points:

1. The increased offloading of cognitive thinking to these tools will disengage our brain from its ability to solve problems on its own. There is enough research across different vocations that proves this. Some that I came across include analysis of the <a href="https://pubmed.ncbi.nlm.nih.gov/32286340/" target="_blank" rel="noopener">impact of GPS usage on our spatial memory</a>, <a href="https://www.semanticscholar.org/paper/The-Retention-of-Manual-Flying-Skills-in-the-Casner-Geven/0552f5c77a6480efd2d96b637516d753358fc870" target="_blank" rel="noopener">retention of manual flying skills as cockpit operations become more automated</a> or something closer to home i.e. <a href="https://www.mdpi.com/2073-431X/14/5/185" target="_blank" rel="noopener">the impact of constantly using coding assistance in problem solving</a>

2. There's an argument to be made here that over time, we've learnt to move above assembly code to languages like C, then we've transitioned to higher level languages and left the complexities of memory management, threading complexities behind. I agree that we must advance, and AI tools are definitely the next step. However, the tools being promoted today are NOT free. At no point in our history has the advancement of computer science depended on proprietary technology. Improvements in frameworks, languages and other tools have been open-sourced and free for a reason. That's the fundamental way for our foundations to progress while we find commercial success with specialised implementations. This is what's best for the ecosystem and has evolved over time.
This is challenged by the current situation where young coders are being asked to lean on Claude Code or GitHub Copilot as their daily driver for all cognition. Also, there is no comparable open-sourced, self-hosted option available that comes close to the same level of output accuracy. Heavy dependency on these tools will not end well for the ecosystem.

With the above in mind, I think **we should consider the following broad directions** in software engineering:

1. Invest in specialised self-hosted or remotely hosted (via remote personal compute) open-sourced models.
Use a common skills framework to enable max developer productivity (somewhat like VSCode with plugins). Encourage developers to use these models and contribute to the model/skill ecosystem.

2. Token-budget based assistance, where developers are encouraged to use AI tools with a certain token limit (intentionally kept low, as current limits from these tools do not align with commercial viability anyway). This incentivises developers to actually grasp the content to avoid being left intellectually stranded once the token limit expires.
At any company that provides Cursor, Copilot or Claude Code plans to their developers, this is absolutely essential for both their cost management and employee skill growth.

3. Explore innovative tooling around test-based development. The argument of LLMs being non-deterministic applies when a layman uses them. However, as software developers, we've always worked with each other (humans being inherently non-deterministic as well) to build amazing things by guardrailing each other as we work on solutions.
Investing in robust testing frameworks will get the best out of coding agents and make them reliable at enterprise scale.

I'm currently learning more about the fundamentals of critical thinking and cognitive development and I plan to write about that soon.