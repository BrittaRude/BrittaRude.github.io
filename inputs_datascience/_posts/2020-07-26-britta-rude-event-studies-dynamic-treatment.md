---
layout: post
usemathjax: true 
title: "Event Studies with heterogeneous treatment effects"
date: 2020-07-23
---

## Event Studies - Application to dynamic events 

In my [previous blogpost](https://brittarude.github.io/blog/2020/07/23/britta-rude-event-studies-and-their-flaws), I have summarized some research about the flaws of event studies. But can we apply our event study design to settings in which treatments happen at different points in time across units? This is exactly what [Sun and Abraham (2020)](http://economics.mit.edu/files/14964) discuss in their working paper. They show that in these kind of settings, the estimate of a given coefficient can be contaminated by the effect from other periods. Therefore, apparent pre-trends can arise from the sole effect of contamination and treatment effect heterogeneity. They derive an alternative estimator, that is free of this contamination. 

They follow [Roth (2020)](https://scholar.harvard.edu/files/jroth/files/roth_pretrends_testing.pdf) and survey the same 12 papers in top economic journals, but this time focusing on staggered difference-in-difference approaches, reporting, what they call _relative period coefficients_ $$ \mu_l $$. [Sun and Abraham (2020)](http://economics.mit.edu/files/14964) focus on cohort-specific average treatment effects, the so-called $$CATT_{e,l}$$, in which a cohort represents all individuals receiving their first treatment $$E_i$$ at the same time $$e$$. They then use these $$CATT_{e,l}$$ to decompose the relative period coefficients $$\mu_l$$. While $$\mu_l$$ should only capture the cohort-average treatment effect from period l, the authors find that this is not the case when parallel trend assumptions are violated. Under the parallel trend assumption, we can describe $$\mu_l$$ as a linear combination of $$CATT_{e,l}$$. The problem with this is that the weights in this linear combination can be non-convex, and even include non-zero weights for relative periods unequal l. These non-zero weights are not what researchers want to show when conducting event studies. 

What does this mean for applied work? It means that we can have a spurious non-zero positive lead coefficient even when there is no pretrend. The contamination of $$ \mu_l $$ from other periods $$l'$$ depends on the relative weights on $$CATT_{e,l'}$$. These weights, on the other hand, depend on the cohort composition. Examining these weights can give researchers some insight about a potential spurious pre-treatment trend. [Sun and Abraham (2020)](http://economics.mit.edu/files/14964) develop an estimator that can accomodate these heterogeneous treatment effects. Their estimation strategy first estimates the heterogeneous treatment effect of each cohort, and then calculates the average of these cohort-specific estimates through applying cohort-specific weights. They then repeat the analysis with the inclusion of covariates.   




