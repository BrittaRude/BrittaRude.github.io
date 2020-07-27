---
layout: post
usemathjax: true 
title: "Staggered DiD versus traditional DiD - What is different"
date: 2020-07-27
---

## Can be apply our traditional DiD strategy to realities with staggered treatment adoption? 

As already addressed in my blogpost about [Staggered Difference-in-Difference](https://brittarude.github.io/blog/2020/07/21/britta-rude-staggered-difference-in-difference-as-the-next-level), policies are often implemented at different points in time for different individuals. It has remained an open question in how far we can apply the insights from the traditional DiD Design to this setting with staggered adoption. Several papers have addressed this question recently. They investigate how estimators in this staggered setting can be interpreted, and find that they are weighted averages of causal effects and bias terms. The literature shows that these weighted averages involve negative terms, and propose alternative estimators, that correct for these negative weights. 

One of these papers is the paper by [Athey and Imbens (2018)](https://www.nber.org/papers/w24963.pdf) which concludes that under random treatment assignment the standard  Difference-In-Differences estimator is an unbiased estimator of a particular weighted average causal effect. The authors also propose an improved variance estimator, since the standard variance estimator is conservative. This blogpost will summarize their paper and the main insights deduced from it. 

## Staggered difference-in-difference: Definition 






