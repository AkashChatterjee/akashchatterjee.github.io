---
layout: post
title: "Glin ML - Locally hosted explainable statistical classifiers for AI agents, over MCP"
date: 2026-09-20
categories: tech
description: "Introducing glin-ml, a local-first tool that trains explainable EBM classifiers and exposes them to Claude and other agents through MCP, keeping your data private while adding statistically grounded reasoning to agentic workflows."
---

Those who were involved in machine learning before the LLM wave took over have a healthy respect for classic ML. A vast majority of real-world problems come down to processing a CSV and outputting a class/bucket with an attached probability. Yes, we've evolved many different advanced techniques to do this, but the core approach and primitives remain the same.

While I've been heavily involved in agentic engineering, I keep switching back to classical ML approaches when I see them fit a use case better than an LLM (or even an SLM) would. The idea is to avoid burning a tremendous amount of compute on a pure classification problem — and in some cases, the output is far superior to an LLM's.

But with many traditional ML algorithms, explainability becomes a problem. That's where LLMs are able to back their outputs with reasoning that's understandable to the user. Note, though, that when an LLM is overloaded with a large number of referenced datapoints, that reasoning can be mathematically incorrect or even fabricated outright. That's the danger of over-relying on an LLM for problems that need large-scale pattern analysis. To solve the explainability issue with traditional ML models instead, there's a class of "glass box" models that's started interesting me lately — Explainable Boosting Machine (EBM) classifiers.

You can read all about them here: [https://interpret.ml/docs/ebm.html](https://interpret.ml/docs/ebm.html)

This opens up an interesting opportunity: statistically accurate predictions backed by exactly how much each parameter contributed to the final score. Couple this with a strong LLM and you unlock capabilities that haven't really been part of the broader AI conversation right now.

As an experiment, I built **glin-ml** — [site](https://akashchatterjee.com/glin/) · [GitHub](https://github.com/AkashChatterjee/glin) · [PyPI](https://pypi.org/project/glin-ml/) — bringing these two approaches together into a simple wrapper. The whole thing runs locally: your training data stays completely private, including from Claude or any other agent, since they only ever get access to the trained model through an MCP server — never the underlying dataset. And because EBMs are so compact, the resulting model itself is tiny; in the example below, the trained classifier weighs in at 5.2 MB on disk, small enough to keep versioned alongside a project or pass around like any other file.

The workflow is simple (more details on the readme link given above):

1. Export or collate your dataset into a CSV with a target class — e.g. a HubSpot export of your closed leads (marked "converted" or "lost" in the status column) along with any number of datapoints.
2. Use the `glin` command to train an EBM classifier with a single command.
3. Add the Glin MCP reference locally to Claude Desktop (or any similar agent).
4. Query Claude — it uses the model, handles the data serialization, gets the prediction results, interprets them, and presents you with an answer backed by real pattern and statistical analysis.
5. Train any number of models on different datasets for different classifications, and access them all through the same Glin MCP.

When experienced professionals answer a complex question in one shot — System 1 thinking, or a "gut feeling" — it's usually pattern matching kicking in from years of experience. That's what we're trying to replicate here, as extremely lightweight models that can be stored and run locally, giving you complete control over your data.

**Here's an example.** Quarter close was three weeks out, and I had ten deals to prioritize — some with full context, some where I was going off memory for revenue and headcount. I asked Claude to run them through a conversion model I'd trained on historical HubSpot deal data and tell me which were actually likely to close versus which I was wasting time on:

> Quarter closes in 3 weeks and I've got 10 deals I'm trying to prioritize. Can you run them through the conversion model and tell me which ones are actually likely to close vs which I'm wasting time on? ... Give me a ranked list with win probability and flag any where a specific factor is dragging the score down. Use the deal_predictor_v1 model and give me a proper reasoning per account.

Claude found the Glin MCP tools, listed the available models, and called `predict` on `deal_predictor_v1` once per deal — passing in structured features like deal amount, pipeline, owner, lifecycle stage, days to close, source, and contact title:

```json
{
  "model_name": "deal_predictor_v1",
  "features": {
    "Amount": 42000,
    "Pipeline": "Enterprise",
    "Deal Owner": "Carla Nguyen",
    "Lifecycle Stage": "Opportunity",
    "Days to Close": 35,
    "Original Source": "Referral",
    "Contact Job Title": "VP of Ops"
  }
}
```

It came back with a ranked table — win probability per deal, plus the specific factors pushing each score up or down:

| Rank | Deal | Win Prob | Verdict | What's dragging/boosting it |
|---|---|---|---|---|
| 1 | Brightpath Manufacturing | 77.9% | Prioritize | Everything favors it: customer lifecycle, owner's rate, 15 activities logged. No drags. |
| 3 | Northwind Systems | 69.2% | Prioritize | Owner, Opportunity stage, VP contact all favor it. Drag: $42k deal size — larger deals skew toward Closed Lost in this model's history. |
| 4 | Ironwood Energy | 69.2% | Prioritize, but watch the clock | Owner and 20+ touches are the biggest boosts. Two drags: $89k size and 90+ days in stage — the strong score doesn't mean it closes in three weeks. |
| 6 | Coastal Health Partners | 56.4% | Borderline — needs activity, not more patience | C-level contact is the single biggest boost in the set. But it drags hard: $110k deal size and only 2 sales activities logged — a big-money deal getting almost no sales motion. |
| 10 | Anchor Nonprofit | 34.4% | Cut it loose | Zero activities, zero contacts, still a Lead — every engagement signal is at rock bottom. The instinct that it's stalled is confirmed by the model, not just vibes. |

What stands out isn't just the ranking — it's the per-deal reasoning grounded in the actual training data. "$110k deal size and only 2 sales activities logged" isn't a generic LLM observation; it's a pattern surfaced directly from the EBM's learned feature contributions, which Claude then wove into a plain-English explanation.

The EBM training flow itself still needs refinement, and that's what I plan to dig into next. But since the whole thing runs fully local — your data never leaves your machine, and the model that does travel is small enough to not think twice about — I'd invite folks to give it a shot. If it seems promising, show the repo some love by starring it, and consider contributing to its capabilities.
