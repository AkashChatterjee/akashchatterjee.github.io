---
layout: post
title: "Setting up a CI/CD Pipeline for AWS Containerized Lambdas via AWS Codepipeline"
date: 2023-07-25
categories: tech
description: "Complete tutorial on setting up CI/CD pipelines for AWS containerized Lambda functions using AWS CodePipeline. Simplify lambda deployments with version control."
---

Implementing code via AWS lambdas has always been a double-edged sword.

It's one of the easiest ways to deploy code but also has its pitfalls when it comes to implementations with complex logic, external library dependencies and version control.

I wanted to document the process of how I implement CI/CD with AWS lambdas from scratch with AWS Codepipeline. The fundamentals remain the same for any other CI/CD tool (e.g. GitHub Actions)

Overall, once you're able to set up the flow, it becomes very easy to manage lambda deployments while coding it in the comfort of your local editor.

<iframe width="560" height="315" src="https://www.youtube.com/embed/1uYPs1GOF-Q?si=-ByC-6NE-U0kAaHS" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
