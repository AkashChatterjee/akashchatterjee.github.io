---
layout: post
title: "Glin ML Case Study: Flagging High Child Stunting Across 704 Indian Districts with 13x Fewer Tokens"
date: 2026-10-11
categories: tech
description: "A case study on NFHS-5 district data: Claude alone vs. Claude with the full training table in the prompt vs. Claude calling a locally trained glin EBM over MCP. Similar accuracy to the table route at a fraction of the tokens, with training data never leaving the machine."
---

In my [previous post](https://akashchatterjee.com/tech/2026/09/20/glin-ml-explainable-classifiers-mcp) I introduced **glin-ml**, a local-first tool that trains an Explainable Boosting Machine (EBM) on your own CSV and exposes it to Claude, or any MCP client, as a tool. This post measures what that buys you compared to just asking the LLM.

**tl;dr:** Claude with glin scored 83.0% accuracy against 75.2% for Claude alone, and matched the route that pastes the whole training table into the prompt, while using **13x fewer tokens**. These are just early days of testing, but the capabilities I'm adding to Glin should give users a very high degree of control when training EBMs and use it with their AI Agent harness of choice.

## The task

- **Data:** 704 Indian districts from the NFHS-5 health survey (2019-20), via NITI Aayog's NDAP portal. Each has 116 indicators (sanitation, women's schooling, antenatal care, anaemia, climate risk and so on) plus a state name.
- **Label:** *high stunting* (35% or more of children under 5 stunted) vs. *lower stunting*. 301 districts (43%) are high. Guessing "lower" every time scores 57%.
- **No leakage:** every child-nutrition outcome was removed from the inputs.
- **Use:** a health team can see where to look first, and why.

## How glin fits in

Train glin once on a CSV on your own machine. Claude then calls `predict` with one district's values, and glin returns the class, the probability and the exact contribution of every factor. Nothing is approximated.

**Example: Madhepura, Bihar.**

- glin gives a 98.2% chance of high stunting.
- The largest factor is sanitation: only 34.6% of people have improved sanitation.
- I asked "what if sanitation were 95%?" and Claude called `predict` again. The chance fell to 97.2%, so the district stays "high".
- Every number matched a direct run of the model.

## Results

I asked Claude Sonnet 5.5 the same question for 141 held-out districts, using a fresh session per district.

| Approach | Accuracy | Tokens per district |
|---|---|---|
| Claude alone (no training data) | 75.2% | 4,155 |
| + all 563 training rows in the prompt | 81.6% | 259,900 |
| + glin | **83.0%** | **20,302** |

Both routes that use the training data beat Claude alone by 6 to 8 points, and the two are equal within noise. But glin needs 13x fewer tokens.

## Why this scales

- **Accurate:** the model learns from every training row, not a few examples in a prompt, and gives the same answer for the same input.
- **Low cost:** you pay to train once. Each question then costs about 20,000 tokens, which depends on the number of indicators, not training rows. The table route grows with every row and at 563 rows already exceeds a 200,000-token context window.
- **Private:** training data stays on your machine. The LLM sees only the one row you ask about, while the table route sends every training row to the model provider on each call.
- **Any harness:** glin speaks MCP. I ran it from Claude Code and from a plain Python client, on both current MCP SDK lines.
- **Explainable:** every answer shows exact factor contributions, so you can ask "why" and "what if" and check the numbers.

## Caveats

- **Correlation, not causation.** glin learned patterns between the indicators and the label, and finding causes was never the goal. The point is that a classification task can be solved cheaply with a small MCP tool and an agent harness, with the model and data staying local.
- **Speed.** The model answers in about 10 ms per district and trains in seconds. But with Claude passing one district at a time, each call took about 16 seconds, against 4 to 5 for a plain prompt, because Claude copies every value into the call. A batch tool would remove most of that delay.

Tested on 2026-10-11 with `claude-sonnet-5-5`. glin-ml is on [GitHub](https://github.com/AkashChatterjee/glin) and [PyPI](https://pypi.org/project/glin-ml/), with a readme on the [project site](https://akashchatterjee.com/glin/). The released PyPI version is v0.1.3, while this case study used v0.1.4 (still under development and release testing).
