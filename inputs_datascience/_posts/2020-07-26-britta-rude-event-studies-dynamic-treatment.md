---
layout: post
usemathjax: true 
title: "Event Studies with heterogeneous treatment effects"
date: 2020-07-26
---

## Event Studies - Application to dynamic events 

In my [previous blogpost](https://brittarude.github.io/blog/2020/07/23/britta-rude-event-studies-and-their-flaws), I have summarized some research about the flaws of event studies. But can we apply our event study design to settings in which treatments happen at different points in time across units? This is exactly what [Sun and Abraham (2020)](http://economics.mit.edu/files/14964) discuss in their working paper. They show that in these kind of settings, the estimate of a given coefficient can be contaminated by the effect from other periods. Therefore, apparent pre-trends can arise from the sole effect of contamination and treatment effect heterogeneity. They derive an alternative estimator, that is free of this contamination. 

They follow [Roth (2020)](https://scholar.harvard.edu/files/jroth/files/roth_pretrends_testing.pdf) and survey the same 12 papers in top economic journals, but this time focusing on staggered difference-in-difference approaches, reporting, what they call _relative period coefficients_ $$ \mu_l $$. [Sun and Abraham (2020)](http://economics.mit.edu/files/14964) focus on cohort-specific average treatment effects, the so-called $$CATT_{e,l}$$, in which a cohort represents all individuals receiving their first treatment $$E_i$$ at the same time $$e$$. They then use these $$CATT_{e,l}$$ to decompose the relative period coefficients $$\mu_l$$. While $$\mu_l$$ should only capture the cohort-average treatment effect from period l, the authors find that this is not the case when parallel trend assumptions are violated. Under the parallel trend assumption, we can describe $$\mu_l$$ as a linear combination of $$CATT_{e,l}$$. The problem with this is that the weights in this linear combination can be non-convex, and even include non-zero weights for relative periods unequal l. These non-zero weights are not what researchers want to show when conducting event studies. 

What does this mean for applied work? It means that we can have a spurious non-zero positive lead coefficient even when there is no pretrend. The contamination of $$ \mu_l $$ from other periods $$l'$$ depends on the relative weights on $$CATT_{e,l'}$$. These weights, on the other hand, depend on the cohort composition. Examining these weights can give researchers some insight about a potential spurious pre-treatment trend. [Sun and Abraham (2020)](http://economics.mit.edu/files/14964) develop an estimator that can accomodate these heterogeneous treatment effects. Their estimation strategy first estimates the heterogeneous treatment effect of each cohort, and then calculates the average of these cohort-specific estimates through applying cohort-specific weights. They then repeat the analysis with the inclusion of covariates. Importantly, in their specification the $$CATT_{e,l'}$$ is the cohort-specific average change in outcome relative to never being treated. This is different to a specification of $$CATT_{e,l'}$$ as the cohort-specific average change in outcome relative to being treatment at a different time period (see [Athey and Imbens (2018)](https://www.nber.org/papers/w24963.pdf). 

[Sun and Abraham (2020)](http://economics.mit.edu/files/14964) show the validity of their estimation method applying it to the research question addressed by [Dobkin et al. (2018)](https://www.aeaweb.org/articles?id=10.1257/aer.20161038) about the economic consequences of hospital admissions for adults. The blogpost at hand will summarize their paper in greater detail. 

## Defining event studies 

[Sun and Abraham (2020)](http://economics.mit.edu/files/14964) define event studies as follows. They focus on absorbing treatment, which means that once the treatment status is switched on, it stays on. They estimate the following equation: 

$$ Y_{it} = Y_{it}^(E_i) = Y{it}^\infty + \sum_{0 \leq e \leq T} (Y_{it}^e - (Y_{it}^\infty ) \times 1{E_i = e} $$, where unit i is first treated in time period e. For never treated units $$E_i = \infty $$. All individuals for which treatment first switches on in period e form one cohort. 








