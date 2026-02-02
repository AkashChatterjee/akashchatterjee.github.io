---
layout: post
title: "EdTech Deep Dive // Make EVERY question count!"
date: 2020-06-22
categories: tech
description: "Using data analysis to improve test questions in EdTech. Learn about discrimination index, distractor analysis, and reliability metrics for MCQ assessments."
---

> "If you do not know how to ask the right question, you discover nothing."
> 
> — W. Edwards Deming

Education is a field that I'm deeply passionate about. During my involvement in various volunteering projects in this domain over the past 3 years, I have always been fascinated by how students learn or comprehend a particular piece of information. As the son of a teacher, I've also have an immense amount of respect for the teaching profession.

At the same time, it is important to acknowledge that the influx of tech into the education sector has been slow and largely need-based. Schools and universities have always aimed to limit their tech footprint to the bare minimum. In most cases, they've settled for a basic platform with rudimentary teacher-student communication support and study resource propagation capabilities. Now, amidst the COVID-19 pandemic, these educational bodies are taking a hard look at themselves and striving to pivot into more tech savvy teaching methodologies. We have a long way to go but it's great to see that the community is finally realizing that technology is not a disruptive force that renders teachers unnecessary. In fact, teachers are the most important cogs in this massive education machinery but technology can exponentially increase the magnitude of their impact on a student. In this post, I aim to cover just one of the many significant metrics that technology, coupled with data, can put in the hands of a teacher.

I'm very new to the EdTech space in a professional capacity. As part of a rapidly growing EdTech startup, I was amazed by the sheer amount of data that we can garner when we deploy a unified, efficient solution at scale. Interested to explore further, I tried to research on how this data can help us take a more strategic approach to examination question creation and analysis.

One of the most popular formats in examinations is the Multiple Choice Questions (MCQ) format. In this format, a student attempts to select the correct answer from the list of options. For this post, we shall be working with this testing format only.

Let's break down the 2 major components of a good test question (Reliability and Validity) and dive into the metrics that'll help us quantify them.

## Reliability

The reliability is usually a metric best detected at a test level than a question level. What we're trying to gauge here is the ability of the test to produce the same approximate results when repeated. Here are certain ways we can try and measure this:

### Test-Retest:

**What?**

The idea is to ensure that when the same test is provided to a select set of students twice, the results do not change drastically. The basic assumptions here are that you're not revealing the score or answers to the test and the students aren't studying the test's topics in the interim period.

**Why?**

Testers can experience some hard-to-measure internal or external / environmental factors when taking a test. This measure helps us account for these factors by giving them a chance to approach the same questions with a different state of mental acuity.

**How?**

A correlation mechanism can be employed here. One example is the Pearson's Correlation Coefficient (r) that we're oh-so-familiar with in the Machine Learning context.

If we assume 100 students took a test 'x' and a retest 'y', then $r_{xy}$ signifies the correlation.

- $n = 100$
- $x_i$ = Score of $i^{th}$ student in test x
- $y_i$ = Score of $i^{th}$ student in test y

The value of $r_{xy}$ will range from -1 to +1. We'd like the score to be as close to +1 as possible.

$$r_{xy} = \frac{n\sum x_iy_i - \sum x_i\sum y_i}{\sqrt{n\sum x_i^2 - (\sum x_i)^2}\sqrt{n\sum y_i^2 - (\sum y_i)^2}}$$

### Parallel Forms:

I'll keep this short. Basically, it's similar to the above but in this case, we change the questions and try to take a retest with different questions that are supposed to test the same thing. The same coefficient based check used in Test-Retest can work here.

### Internal Consistency:

**What?**

An internal consistency check helps us understand if all questions in a test that are based on the common concept are receiving comparable responses (almost all right or almost all wrong). For example, if we're testing the concept Viscosity in the chapter Fluid Dynamics with 2 questions on a Physics test, we'd be expecting a student to get either both right or both wrong.

**Why?**

We'd like to understand if we have the drilled down to the right concepts when formulating the questions. In this way, if a student scores low, we're able to provide some remediation for that particular topic. Now, you might say that it's obviously possible for a student to not know about a certain aspect of Viscosity in the example I gave. This measure then helps us segregate that concept into finer details and target a student's weakness in a more efficient manner.

**How?**

We split the test into chunks of 3 (for larger tests) or 2 (for smaller tests) with each chunk containing questions for a particular concept. After the test, apply the correlation coefficient described in Test-Retest across the sections and check for reliability.

If the entire test is judging a single competency / concept, we can use the **Kuder-Richardson Formula** to check reliability.

- $k$ = Total number of questions
- $p_j$ = Number of students who got question j correct
- $q_j$ = Number of students who got question j wrong
- $\sigma^2$ = Variance of the total scores of all students

The Kuder-Richardson score ranges from 0 to +1. We'd want the score to be somewhere in the 0.7-0.9 range. A score above 0.9 signifies very high homogeneity in a test but depending upon your use-case, this could be good or bad.

$$KR_{20} = \frac{k}{k-1}\left(1 - \frac{\sum p_jq_j}{\sigma^2}\right)$$

Another popular technique is using multiple test raters to avoid bias in test validation but that doesn't really apply to MCQs because the answer isn't subjective. However, asking multiple teachers to take the same test without being aware of the answers would be a good way to check reliability.

## Validity

The second major aspect of appraising a question or a test is understanding how effective it is in achieving the required result i.e. measuring what it's supposed to measure.

Let's identify the metrics in this area:

### Discrimination Index:

**What?**

In my opinion, this is a very crucial factor. This index helps us detect if the answering patterns for a question is behaving as expected at scale. We are able to check if the top performers of a test are getting a question right while the bottom performers are getting it wrong.

**Why?**

A good question should always act as a discriminator i.e. it should be able to separate the top performers in a test from the bottom performers. If a question is unable to do so and worse, if it's consistently marked wrong by top performers but marked correct by bottom performers, we'd like to investigate this question and ideally, remove it from the test.

**How?**

The average scores I see in practice for various assessments show a variety of patterns. But for the sake of simplicity, I shall be assuming a normal distribution. In most cases, we do see similarity with a normal curve but the calculations might have to be slightly altered on a case-to-case basis.

Various studies have shown that taking 27% of students from the top and bottom gives us a reliable sample set for our index. What we're trying to do here is to take as many data points as possible while still maintaining a significant amount of difference in characteristics between the top and bottom performers.

- $N$ = Total Students
- $T$ = Top 27% performers in a test
- $B$ = Bottom 27% performers in a test

Discrimination Index (DI) is then given by these equations:

**Version 1:**
$$DI = \frac{T - B}{MAX(T,B)}$$

**Version 2:**
$$DI = \frac{T - B}{N}$$

**Version 3:**
$$DI = \frac{T - B}{T + B}$$

**NOTE:** The denominator can take various forms based on how much you want to penalize the metric based on unwanted results. For example, you can also choose to divide by a sum of T + B or by N instead of taking a MAX. This would mean that you're penalizing by subtracting B from T as well as taking its value into consideration when the division takes place. Depending on your results, you can stick to a particular version of the calculation.

The value ranges from -1 to +1. We'd like the value to be as close to +1 as possible. Any value less that 0 must be investigated. For the first version, you would have to handle for a division by zero scenario.

### Distractor Analysis

**What?**

Distractor analysis helps us take the concept of Discrimination Index to the MCQ's option level. It helps us understand if a particular wrong option is acting as a distractor in a question i.e. we receive a significantly high number of selections for that option even though it is not the correct answer.

**Why?**

The main goal behind it is to analyze good distractor options that might look like the correct answer to bottom performers but is ignored by top performers. This helps us add an extra dimension to our question's effectiveness analysis.

**How?**

Let's take an example here.

**Q:** What is the value of pi accurate to 2 decimal places?

**Options:**
- a) 3.14 (correct answer)
- b) 1.73
- c) 3.99
- d) 2.32

- Number of top performers (T) = 30
- Number of bottom performers (B) = 30
- Sum (T') = T
- Sum (B') = B
- The option-level Discrimination Index = (T' – B')/(T' + B')

In the example given below, we see how option 'b' has acted as an efficient distractor as it is drawing the lower performers away from the correct answer.

| Options | Top Performers (T') | Bottom Performers (B') | Total (T'+B') | Option-level Discrimination Index |
|---------|---------------------|------------------------|---------------|-----------------------------------|
| a       | 20                  | 2                      | 22            | +0.81                             |
| b       | 5                   | 26                     | 31            | -0.68                             |
| c       | 2                   | 1                      | 3             | 0.33                              |
| d       | 3                   | 1                      | 4             | 0.50                              |

### Difficulty Index

For the sake of completion, I'll mention this index. However, it's a fairly simple first level analysis. Difficulty Index is simple the number of students who got a question wrong divided by the total number of students who've attempted the question.

The value would range from 0 to +1. An ideal question in a difficult test should lie between 0.1 to 0.2. Although a value of 0.0 to 0.1 would signify a very tough question, it might also mean that answering that particular question is not feasible within the constraints of a test.

---

There's still a lot to try out and learn about these metrics. To keep the authenticity of the insight shared in this post, I've tried to limit myself to factors I've been able to research and in most cases, try out on some actual data sets. However, there are countless other metrics that might suit your custom need. For example, lookup Cronbach's Alpha as a measure of a test's internal consistency.

It's of paramount importance that educational institutions invest in broadening their data analysis mechanisms and build a continuous feedback loop, which helps them create a strong curriculum. The punchline here is that even without setting up a complex data science infrastructure, simple data points can make a world of difference in the education domain.

**EVERY question matters. So, make it count.**
