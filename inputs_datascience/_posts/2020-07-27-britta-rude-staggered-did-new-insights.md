---
layout: post
usemathjax: true 
title: "Staggered DiD versus traditional DiD - What is different?"
date: 2020-07-27
---

## Can be apply our traditional DiD strategy to realities with staggered treatment adoption? 

As already addressed in my blogpost about [Staggered Difference-in-Difference](https://brittarude.github.io/blog/2020/07/21/britta-rude-staggered-difference-in-difference-as-the-next-level), policies are often implemented at different points in time for different individuals. It has remained an open question in how far we can apply the insights from the traditional DiD Design to this setting with staggered adoption. Several papers have addressed this question recently. They investigate how estimators in this staggered setting can be interpreted, and find that they are weighted averages of causal effects and bias terms. The literature shows that these weighted averages involve negative terms, and propose alternative estimators, that correct for these negative weights. 

One of these papers is the paper by [Athey and Imbens (2018)](https://www.nber.org/papers/w24963.pdf) which concludes that under random treatment assignment the standard  Difference-In-Differences estimator is an unbiased estimator of a particular weighted average causal effect. The authors also propose an improved variance estimator, since the standard variance estimator is conservative. This blogpost will summarize their paper and the main insights deduced from it. 

## Staggered difference-in-difference: Definition 

To derive their results, [Athey and Imbens (2018)](https://www.nber.org/papers/w24963.pdf) consider a setting with i=1,...,N individuals and t=1,...,T time periods, with a binary treatment being first adopted in t=T+1. $$Y_{it}(a)$$ is the outcome of interest, and Ai is the adoption date. If an individual does not adopt the treatment, the treatment period is $$T= \infty $$. They define $$ W(a,t) = 1_{a \leq t} $$ as an indicator for the adoption date a before t. They then derive a matrix W that contains never adopters, late adopters, medium adopters and early adopters. They then define a fraction of individuals belonging to each timing-treatment-group, and the  population  average  of the potential outcome in period t for adoption date a:

$$ Y_t(a) = \frac{1}{N} \sum_{i=1}^N Y_{it}(a)$$ 

and the average causal effect of adoption date a′ relative to a, on the outcome in period t as: 

$$ \tau_{t, aa'} = Y_t(a') - Y_t(a) = \frac{1}{N} \sum_{i=1}^N \{Y_{it}(a') - Y_{it}(a) \}$$. 

This is slightly different from the $$CATT_{a,i}$$ definition that Abraham and Sun use in their paper which I discussed in my [previous blogpost](https://brittarude.github.io/blog/2020/07/26/britta-rude-event-studies-dynamic-treatment). 

## Assumptions 

[Athey and Imbens (2018)](https://www.nber.org/papers/w24963.pdf) derive three sets of assumption, which I will summarize in the following: 

- Assumption 1: Random adoption date. This assumption is very strong, but can only be relaxed through additional information, as for example the inclusion of additional control variables, pre-treatment variables, or limiting treatment effects. 

- Assumption 2: Exclusion restrictions. The first of the two exclusion restriction is that future adoption dates do not affect current outcomes. The assumption is also called no-anticipation assumption. The second exclusio restriction is the invariance-to-history assumption. The assumption implies that for the outcome observed in period t it does not matter how long the individual has been exposed to a certain treatment, just whether it has been exposed, or not. Put differently, there is no causal effect of an early or late adoption of the treatment on the outcome of interest. [Athey and Imbens (2018)](https://www.nber.org/papers/w24963.pdf) give an example of this exclusion restriction, that is, when our cohorts differ per period (pooled panel data), as for example in the case that we estimate the effect of a student subsidy on the employment rate of 25-year olds. Then it makes sense to assume that the college choice of students graduating depends on the current subsidy, but not on the subsidy during previous years. 

- Assumption 3-5: Auxiliary assumptions. The first of these auxiliary assumptions is the assumption about constant treatment effects across units. It implies that the effect of adoption date a' is the same for all units. The second auxiliary assumption states that treatment effects are constant over time. The third auxiliary assumption imposes random sampling, and the last one additivity. 

## Defining and interpreting the DiD estimator 

[Athey and Imbens (2018)](https://www.nber.org/papers/w24963.pdf) in a next step derive the difference-in-difference estimator in the staggered design, and analyze its interpretation. They define the regression estimation as follows: 

$$ Y_{it} = \alpha_i + \beta_t + \tau W_{it} + \epsilon_{it} $$, 

with $$ \tau $$ being our main variable of interest, assuming treatment effects to be additive and constant across time periods and units. They interprete the DiD estimand under the random adoption assumption, therefore leading to a different result than previous paper analyzing the estimate. 










