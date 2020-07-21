---
layout: post
title: "Staggered Difference-in-Difference - What is different?"
date: 2020-07-21
---

## Staggered Difference-in-Difference: What does it mean?

Often, a policy is introduced in many different states during many different time periods. This is what researchers refer to when they talk about a staggered Difference-in-Difference Design, or a generalized Difference-in-Difference. Let's have a look at the food stamp program. The food stamp program was rolled out on a county-by-county basis between 1962 and 1975. The program provided low-income families with food vouchers that could be used at grocery stores. A paper by [Hoynes et al. (2016)](https://www.aeaweb.org/articles?id=10.1257/aer.20130375) uses this _staggered_ roll-out as their identification strategy to assess the long-run effects of childhood access to the safety net. They analyze the participation in the program, and with this, an increase in income and resources, on health and economic outcomes in the long-run. Their main treatment variable is the time period that children were subject to the program between conception and the age of 5. The graph below from their online appendix shows the staggered implementation of the Food Stamp Program (FSP) over time (Note: The numbers in the figure is weighted by the 1970 county population): 

<img src="/images/Rollout_FSP.PNG" alt="Parallel Trend Assumption" style="max-width:60%;"/>

The image shows that the FSP started with a pilot program in 1961, a pilot expansion phase from 1962 to 1963 to 43 counties, and the Food Stamp Act of 1964, giving all states access to the program, leading to a steady stream of counties implementing the FSP from 1964 onward. The FSP became mandatory in 1975. The authors use this variation as their identification strategy, with the month of FSP adoption representing the start of the treatment. 

## Staggered Difference-in-Difference: When does it work? 

Similarly to the traditional Difference-in-Difference strategy with one period and one treatment and control group, the staggered DiD relies on important assumptions. The most important assumption is the exogeneity assumption. The identification strategy holds, if the rollout is exogenous, that is randomly implemented over time, and not related to variables important for our outcome of interest, or related to the outcome of interest itself. If, for example, the FSP in its early phase was only implemented in areas with low health conditions, and later in areas with high health conditions, our treatment and control groups systematically differ from each other. Or if it first was implemented in areas with a high poverty share, and then in areas with a low poverty share. These differences in county characteristics that influence the adoption timing could result in spurious correlation, if these same county characteristics are related to differential trends in our outcome of interest. The identification assumption is that there are no contemporaneous county level trends that are correlated with the introduction of the FSP and our outcomes of interest at the same time (health and economic outcomes). 

How do we test this assumption? First, we can run a regression, in which the adoption of our FSP is the outcome variable, and all kind of covariates are introduced as potential explanatory variables. 






