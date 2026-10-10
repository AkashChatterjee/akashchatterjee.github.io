---
layout: post
title: "Glin ML Case Study: Flagging High Child Stunting Across 704 Indian Districts with 13x Fewer Tokens"
date: 2026-10-11
categories: tech
description: "A case study on NFHS-5 district data: Claude alone vs. Claude with the full training table in the prompt vs. Claude calling a locally trained glin EBM over MCP. Similar accuracy to the table route at a fraction of the tokens, with training data never leaving the machine."
---

In my [previous post](https://akashchatterjee.com/tech/2026/09/20/glin-ml-explainable-classifiers-mcp.html) I introduced **glin-ml**, a local-first tool that trains an Explainable Boosting Machine (EBM) on your own CSV and exposes it to Claude, or any MCP client, as a tool. The pitch was that you get statistically grounded, explainable predictions without handing your dataset to anyone. What I didn't have was a proper measurement of what that buys you compared to just asking the LLM.

So here is one. I picked a real public-health dataset, a binary classification task, and ran the same question three different ways.

## The data

704 Indian districts from the NFHS-5 health survey (2019-20), sourced from NITI Aayog's NDAP portal. Each district has 116 indicators (sanitation, women's schooling, antenatal care, anaemia, climate risk and so on) plus a state name. 301 of the 704 districts, about 43%, have high stunting.

## The goal

For each district, predict one label: **high stunting** (35% or more of children under 5 are stunted) or **lower stunting**. A health team could use this to decide where to look first, and why.

The label comes from the same survey, but I removed every child-nutrition outcome from the inputs, so the model can't just read the answer off a neighbouring column. Guessing "lower" for every district scores 57%, which is the number to beat.

## How glin fits in

You train glin once, on a CSV on your own machine, and it builds an EBM. Claude then calls `predict` with one district's values and glin returns the class, the probability, and the exact contribution of every factor. Nothing is approximated.

**Example: Madhepura, Bihar.** glin gives a 98.2% chance of high stunting. The largest factor is sanitation: only 34.6% of people have improved sanitation. I asked "what if sanitation were 95%?" and Claude called `predict` again. The chance fell to 97.2%, so the district stays "high". Every number matched a direct run of the model.

That "why" and "what if" loop is the part an LLM can't do reliably on its own, and it's the part EBMs are built for.

## What I measured

I asked Claude Sonnet 5.5 the same question for 141 held-out districts, in three ways, with a fresh session for each district:

1. **Claude alone**, with no training data.
2. **Claude plus the training table** pasted into the prompt, all 563 training rows.
3. **Claude plus glin**, calling the trained model over MCP.

| Approach | Accuracy | Tokens per district |
|---|---|---|
| Claude alone | 75.2% | 4,155 |
| + training table in the prompt | 81.6% | 259,900 |
| + glin | 83.0% | 20,302 |

Both routes that use the training data beat Claude alone by 6 to 8 points. The table route and the glin route are equal within noise. But glin needs **13 times fewer tokens** to get there.

## Why this scales

- **Accurate.** The model learns from every training row, not from a handful of examples squeezed into a prompt, and it gives the same answer for the same input every time.
- **Low cost.** You pay to train once. After that, each question costs about 20,000 tokens, and that number depends on the count of indicators, not the count of training rows. The table route costs 260,000 tokens at 563 rows and grows with every row you add. At this size it already exceeds a 200,000-token context window.
- **Private.** Training data stays on your machine and glin runs locally. The language model sees only the one row you ask about. The table route sends every training row to the model provider on every call.
- **Any harness.** glin speaks MCP, so any MCP client can call the same trained model. I ran it from Claude Code and from a plain Python client, on both current MCP SDK lines.
- **Explainable.** Each answer comes with exact factor contributions, so you can ask "why" and "what if" and check the numbers.

## Caveats worth stating plainly

**This is correlation, not causation.** glin learned patterns between the indicators and the stunting label. That says nothing about what causes stunting, and finding causes was never the goal. The goal was to show that a classification task can be solved cheaply with a small MCP tool and an agent harness, while the model and training data stay on your own machine. Only the agent calling glin can run elsewhere.

**Speed is the weak spot.** The glin model answers in about 10 milliseconds per district and trains in seconds. But with Claude passing one district at a time, each call took about 16 seconds, against 4 to 5 seconds for a plain prompt, because Claude has to copy every value into the call. A batch tool would remove most of that delay, and it's high on my list for glin.

**Check the data terms.** The data is from NDAP (NITI Aayog) and the NFHS-5 survey (MoHFW/IIPS). Check NDAP's terms before sharing the data itself; this post shows aggregate results only.

Tested on 2026-10-11 with `claude-sonnet-5-5`. If you want to try this on your own data, glin-ml is on [GitHub](https://github.com/AkashChatterjee/glin) and [PyPI](https://pypi.org/project/glin-ml/), with a readme on the [project site](https://akashchatterjee.com/glin/). Just FYI, the released PyPI version is v0.1.3 while the version used for this case study was v0.1.4 (current still under development + release testing).
