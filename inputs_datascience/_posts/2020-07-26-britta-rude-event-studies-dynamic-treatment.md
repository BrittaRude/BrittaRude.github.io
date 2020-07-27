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

$$ Y_{it} = Y_{it}^E_i = Y{it}^\infty + \sum_{0 \leq e \leq T} (Y_{it}^e - Y_{it}^\infty ) \times 1 (E_i = e) $$, 

where unit i is first treated in time period e. For never treated units $$E_i = \infty $$. All individuals for which treatment first switches on in period e form one cohort. It is important to stress that the authors define the treatment effect as the difference between the outcome of treated individuals $$Y_{it}$$ relative to the outcome of never treated units $$Y_{it}^\infty$$. They then define the $$CATT_{e,l}$$ (the cohort-specific average treatment effect on the treated) as the average of all treatment effects of all individuals receiving treatment for the first time at period e. They estimate the CATT not at time period t, but at relative time period l (l periods from the initial treatment e): 

$$ CATT_{e,l} = E(Y_{i,e+l} - Y_{i,e+l}^\infty | E_i = e) $$

They shift from calendar time t to relative period l as they then can compare across cohorts which received the treatment for the first time at different time periods e, while holding their exposure to the treatment constant. 

## Identifying assumptions 

There are three identifying assumptions in this set-up: 

- __Assumption 1:__ Parallel trends in baseline outcomes: This is a generalization of the parallel trend assumption we already know from our canonical DiD Set-Up, and implies that parallel trends should hold for all units receiving treatment for the first time at different periods e, and also for never treated units. 

- __Assumption 2:__ No anticipatory behavior in $$ l \in g $$ periods prior to treatment. This means, that outcomes prior to the treatment should be equal to the baseline outcome, on average. If this assumption holds, then there are no pre-trends, as $$CATT_{e,l} = 0$$. This assumption is similar to the no-manipulation of treatment status assumption in the Regression Discontinuity Design. This is most likely to hold if individuals cannot predict their future treatment status. 

- __Assumption 3:__ Treatment effect homogeneity: For each relative period l, $$CATT_{e,l}$$ does not depend on the timing of its first treatment status e, and is equal to $$CATT_l$$. This means that treatment effects are the same across different cohorts for every relative treatment period l. This means that different cohorts share the same treatment path, and does not mean that dynamic treatment effects do not occur. A variation across cohorts could stem from differing covariates across cohorts (e.g. treatment varies by age, and cohorts vary by their age structure), calendar-time varying effects (e.g. macroeconomic conditions), or individuals selecting into treatment at certain times e, that is, the treatment timing is not random. The last case still satisfies their parallel trend assumpion, if it is not determined by the outcome. 

## Estimators - How to interprete coefficients in the dynamic set-up 

The traditional event study regression, also called two-way (unit and time) fixed effects regression, with i=1, ..., N units and t=1, ..., T calendar time periods looks as follows: 

$$Y_{i,t} = \alpha_i + \gamma_t + \sum_{g \in G} \mu_g 1{t-E_i \in g} + \nu_{i,t}$$, 

where $$Y_{i,t}$$ is the outcome of interest for unit i at time t, and $$g \in G§§ are relative periods. $$ \mu_g $$ are the relative period coefficients, and ideally these should be convex averages of $$CATT_{e,l}$$. [Sun and Abraham (2020)](http://economics.mit.edu/files/14964) have a closer look at the relative period indicator $$ 1{t-E_i \in g} $$ to see how it varies with time. They define the following as an indicator for unit i being observed l periods away from the initial treatment at calendar time t: 

$$ 1{t-E_i \in g} = 1{t-E_i = l} = \sum_{l \in g} D_{i,t}^l $$ 

This indicator can be applied to a static or dynamic set-up. The static set-up assumes that $$ g= [0, T] $$ and that $$ 1{t-E_i \in g} = 1{E_i \leq t} $$ (has unit i received its initial treatment by calendar-time period t?). The static regression thus looks as follows: 

$$Y_{i,t} = \alpha_i + \gamma_t + \mu_g \sum_{l \geq 0} D_{i,t}^l + \nu_{i,t}$$, 










