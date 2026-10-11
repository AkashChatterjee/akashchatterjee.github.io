---
layout: post
title: "Glin ML Case Study: Segmenting 100,000 Sales Leads with Claude and an Explainable Boosting Machine"
date: 2026-10-11
categories: tech
description: "A case study on synthetic CRM data with known rules: Claude alone vs. Claude with 1,000 labeled leads in the prompt vs. Claude calling a locally trained glin EBM over MCP. Higher accuracy, 6.7x fewer tokens, and the same answer in every session."
---

glin trains an Explainable Boosting Machine (EBM) on your table and gives it to Claude as an MCP tool. This case study measures what that gives you, compared with Claude alone. All data is synthetic, so the true answer is known for each lead.

## Summary

| Approach | Right segment | Tokens for each lead | Same answer in 3 sessions |
|---|---|---|---|
| Claude alone | 33% | 2,000 | 88% |
| Claude with 1,000 labeled leads in the prompt | 60% | 83,000 | 78% |
| Claude with glin, 2 models | 70% | 12,800 | 98% |
| **Claude with glin, 1 model** | **71%** | **12,300** | **100%** |

- Claude with glin got 11 points more than Claude with the pasted table. It used 6.7 times fewer tokens.
- The best possible score on these leads is 68%. glin is at the limit of the data.
- glin gave the same answer for the same lead in each new session.
- In a what-if test, glin was correct to within 3 points of the true effect of each sales action.
- One 4-class model and two binary models gave the same accuracy.

## The task

A B2B software company has 100,000 leads in its CRM. For each new lead, it wants to know one of four segments:

| Segment | Meaning |
|---|---|
| high_value_converted | The account is $50k a year or more, and it became a customer in 120 days |
| high_value_not_converted | The account is $50k a year or more, and it did not become a customer |
| low_value_converted | The account is less than $50k a year, and it became a customer |
| low_value_not_converted | The account is less than $50k a year, and it did not become a customer |

Each lead has 23 columns. Examples are company size, revenue, industry, lead source, demo attended, budget confirmed, discount requested and days since first touch.

## Why synthetic data

The [first glin case study](https://akashchatterjee.com/tech/2026/10/11/glin-ml-case-study-child-stunting-nfhs5) used real survey data (NFHS-5). Claude already knew much about that subject, so glin added only 6 to 8 points. Also, real data has no known answer, so you cannot check the explanations of a model.

For this study, I wrote the rules that make the data. The rules are specific to this company, so Claude cannot know them. Because I know the rules, I can check each prediction, explanation and what-if against the truth.

### The rules in the data

Different columns control value and conversion:

- Company size, revenue, industry, job level, features requested and an SSO requirement control **value**.
- Lead source, demo, budget, competitor, pricing-page visits, trial users, rep tenure and time in the pipeline control **conversion**.

Some conversion rules are not what a sales manager expects:

- More stakeholders help, up to 4 or 5 people. A group of 6 or more stops the deal.
- Webinar leads convert less than content downloads.
- A discount request of up to 25% has no effect. Above 25%, each point decreases the chance of a sale.
- After 90 days, the chance of a sale decreases quickly.
- A hidden "urgency" value increases both value and conversion. This value is not in the data. It connects the two outcomes.

### The problems in the data

The CRM export is not clean, as in real work:

- Revenue is in three formats: `$12,500,000`, `USD 12,500,000` and `12500000`.
- Percents have a `%` sign. Dates are in two formats. Leads without a trial show `n/a`.
- The `crm_stage` column is set after the outcome. It gives the answer (target leakage).
- The `lead_id` column is an identifier.
- Two columns have no effect. They only follow company size, so they look useful.

## Step 1: Claude trains the models

I gave a new Claude session the training file (90,000 leads), the glin MCP tools and Python. I asked it to compare one 4-class model with two models (value and conversion), to train both and to recommend one.

The session took 7.3 minutes and cost $0.20. It did these steps:

1. It found that `crm_stage` gives the answer, and it removed that column and `lead_id`.
2. It trained three glin models: `seg4` (4 classes), `value_tier` and `converted`.
3. It recommended two models, and it said that the result was close.
4. It found that conversion increases at the end of each quarter.
5. It found that the two columns without effect get almost no weight.
6. It found a fault in my data. In some leads, `days_since_first_touch` does not agree with `first_touch_date`. The session told me to examine this column. In a real CRM, this is the correct question to ask.

## Step 2: Claude answers for 200 new leads

For each of 200 test leads, a new Claude session predicted the segment. Each session saw only the columns of one lead.

| Approach | Right segment | Value right | Conversion right | Cost for each lead |
|---|---|---|---|---|
| Claude alone | 33% | 58% | 55% | $0.010 |
| Claude with 1,000 leads in the prompt | 60% | 81% | 72% | $0.033 (cache in use), $0.34 (no cache) |
| Claude with glin, 2 models | 70% | 85% | 83% | $0.029 |
| Claude with glin, 1 model | 71% | 87% | 82% | $0.024 |

Random selection gives 25%. On all 10,000 test leads, both glin designs got 69.2%, which is equal to the best possible score.

Claude used the glin result correctly in all 400 glin sessions. Each prediction in a session was the same as a direct call to the model.

## Step 3: the same answer in each session

I sent the same 40 leads three times, each time in a new session.

- Claude alone changed its answer for 1 lead in 8.
- Claude with the pasted table changed its answer for about 1 lead in 4.
- Claude with glin (1 model) gave the same answer each time.

With 2 models, one lead had a different answer one time. In that session, Claude also used an old test model that the training session did not delete. Delete old models, or give them clear names.

## Step 4: the explanations are correct

I compared the effect that glin learned for each column with the true effect.

- For all nine rules that I checked, the correlation between the learned effect and the true effect is 0.97 to 1.00.
- glin found each limit and each change of direction. Examples are the stop at 6 stakeholders and the drop above a 25% discount.
- The learned effects are approximately 0.8 times the true size. The hidden urgency value causes this. The shape and the order of the effects are correct.
- The two columns without effect get less than 0.5% of the total importance.

## Step 5: what must the sales rep do?

I selected one open lead: a finance company with 537 employees, no demo, budget not known and a 34% discount request. I asked which action increases the chance of a sale most: confirm the budget, give a demo or decrease the discount to 10%.

| Action | True chance | Claude with glin | Claude with 1,000 leads | Claude alone |
|---|---|---|---|---|
| No change | 27% | 29% | 30% | 7% |
| Discount decreased to 10% | 56% | 56% | 36% | 10% |
| Budget confirmed | 63% | 66% | 42% | 15% |
| Demo attended | 67% | 69% | 47% | 13% |
| Budget and demo | 91% | 91% | 58% | 24% |

- Claude with glin called `predict` once for each action. All of its values are within 3 points of the truth. It selected the demo first, which is correct.
- Claude with the pasted table selected the same action, but its effects are approximately half the true size.
- Claude alone put the lead in the wrong segment. Its values are 20 to 67 points too low.

## One model or two models?

| | 1 model (4 classes) | 2 models (value and conversion) |
|---|---|---|
| Accuracy on 10,000 leads | 69.2% | 69.2% |
| Log loss (lower is better) | 0.716 | 0.721 |
| Training time, 90,000 leads | 248 s | 127 s |
| `predict` calls for each lead | 1 | 2 |
| Explanation | One list of causes for the segment | One list for value and one list for conversion |

- The accuracy is the same.
- One model has slightly better probabilities, because it learns the connection between the two outcomes. It also needs only one tool call.
- Two models train in half the time. They give a separate explanation for each outcome. Sales can use the conversion model, and finance can use the value model.
- Use two models when people act on each outcome. Use one model when you need only the segment.

## Why this is better at scale

- **Cost.** A pasted table costs approximately 81 tokens for each row in each question. All 90,000 leads need approximately 7.3 million tokens, which is much more than a context window. glin uses approximately 12,500 tokens for each question, for any quantity of data. You train the model one time.
- **Accuracy.** glin learns from all 90,000 leads. A pasted table shows Claude only a small sample.
- **Privacy.** The training data stays on your computer. Claude sees only the lead in the question.
- **Consistency.** The same lead gets the same answer in each session and for each user.
- **Explanation.** Each prediction shows the exact contribution of each column, so you can check the answer to "why" and "what if".

## Limits of this study

- The data is synthetic. On real data, Claude can know more about the subject, and the difference can be smaller.
- The test has 200 leads for each approach. A difference of less than approximately 7 points is not certain.
- A different pasted sample, more rows or better instructions can change the result of the pasted-table approach.
- The rules in this data are causes, because I wrote them. On real data, a model finds associations, not causes. However, that's where this data-backed output can let a smart model like Claude Opus 5.5 think through causality now that it has statistically accurate correlations.
- A glin session takes 5 to 7 seconds for each lead. Claude alone takes 3 seconds. Claude uses most of this time to copy the columns of the lead into the tool call.

The test ran on 2026-10-11 with `claude-sonnet-5-5` and glin-ml 0.1.4 from PyPI. The 1,128 Claude sessions cost $25.08 in total.

- GitHub: [AkashChatterjee/glin](https://github.com/AkashChatterjee/glin)
- PyPI: [glin-ml](https://pypi.org/project/glin-ml/)
