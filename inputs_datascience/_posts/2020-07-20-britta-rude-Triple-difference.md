---
layout: post
usemathjax: true 
title: "Triple Difference - Why Triple?"
date: 2020-07-20
image: /images/DataScience_FeatureImage.jpg
---

## Triple Difference: A bicycle program for girls in India


In my [previous blogpost](https://brittarude.github.io/blog/2020/07/18/britta-rude-revisiting-difference-in-difference) I talked about the famous Difference-in-Difference strategy, and new insights about it. Often, to further stress the robustness of their results, researchers apply a Triple-Difference strategy. In this blogpost I want to give a short overview on what this Triple-Difference is about, and what do we need it for. 

To understand what the Triple-Difference estimate is about, let's have a look at another nice paper by [Muralidharan and Prakash (2017)](https://www.aeaweb.org/articles?id=10.1257/app.20160004). 
They study the impact of providing girls with bicycles on secondary school enrollment. 
The program was only implemented in the Indian state of Bihar. We can then apply a Difference-in-Difference strategy using girls of the neighboring state Jharkhand as a control group (Jharkhand was part of Bihar for over 50 years and is therefore considered a quite good control state). 
But we can even go further, and compare the difference in secondary enrollment between girls across both states before and after the introduction of the bycicle program in Bihar to the same difference between boys across both states before and after the provision of bycicles to girls in Bihar. 
This is then our third difference, leading us to the so-called Triple-Difference estimation strategy. Why is it convenient to apply this Difference-in-Difference-in-Difference strategy? The Triple-Difference strategy can be helpful when our famous parallel trend assumption is violated. 
In the paper by [Muralidharan and Prakash (2017)](https://www.aeaweb.org/articles?id=10.1257/app.20160004) the parallel trend assumption does not hold, as the bicycle program was introduced in a time of rapid growth and a sharp increase in public spending for education in the state of Bihar. 
This is not the case for the control state Jharkhand. Neither does the parallel trend assumption hold when we compare boys and girls within the steate of Bihar. The triple-difference strategy solves both problems. 

The [Best Practice Guide by Wing et al. (2018)](https://www.annualreviews.org/doi/pdf/10.1146/annurev-publhealth-040617-013507) gives a nice explanation about what it is all about. The core of the problem lies with time-varying confounders that develop differently across our treatment and control group (or state in this case). This is what economists call a time-varying confounder that is not state-invariant. The solution is to find an additional control group, that is not subject to the treatment in state A, but to the time-varying confounder. In our example, the comparison of boys belonging to the young and old cohort, and living in Bihar and Jharkhand, represents an estimate of the state-varying confounder free from our treatment, while the comparison of girls belonging to the young and old cohort, and livign in Bihar and Jharkhand, represents a combination of our treatment effect and the state-varying confounder. When we subtract the former from the letter, we get our treatment effect free from the state-varying confounder. 

The [Best Practice Guide by Wing et al. (2018)](https://www.annualreviews.org/doi/pdf/10.1146/annurev-publhealth-040617-013507) gives an easy to understand example. Suppose that state A introduces a tax on large hospitals, but not small ones, and we want to estimate its effect on nurses' wages. Let's further assume that the tax-adopting states are marked by regional economic booms and recessions. The standard Difference-in-Difference design then interprets these geographic differences as part of our DiD-estimator. In contrast, if we take small hospitals from state A and B as an additional control group, we correct for this behavior.  

## Summing up: Three differences

Coming back to the paper by [Muralidharan and Prakash (2017)](https://www.aeaweb.org/articles?id=10.1257/app.20160004), to some up, they generate three differences through their estimtaion strategy:

a) Our first difference: Secondary enrollment between female cohorts affected by the program in the state of Bihar (girls aged 14 or 15 (our treatment cohort) versus girls aged 16 to 17 (our control cohort)). But what about possible confounders that changes within Bihar during the same time that our bicycle program was rolled out? We control for this by introducing:

b) Our second difference: Secondary enrollment between male cohorts of the same age (boys aged 14 or 15 and boys aged 16 and 17). They are good control groups as they have been exposed to the same changes that took place within Bihar across time as girls, without having been eligible for the program. 
Still, we have a large difference in levels, and as explained in one of my [previous blogpost](https://brittarude.github.io/blog/2020/07/18/britta-rude-revisiting-difference-in-difference) this could be problematic to our identification strategy. 
Probably, as their secondary enrollment was much lower, the growth in secondary enrollment was much higher for girls than boys. 
The authors test for parallel trends in secondary enrollment for the years am (2002-2003 to 2005-2006) and reject the parallel trend assumption. Therefore, they introduce: 

c) Our third difference: This means, comparing the double difference generated by a) and b) in the state of Bihar to the same double difference in the control state  of Jharkhand. 
They again test for parallel trends of the triple difference, and do not reject it. Problem solved. 

## How do we estimate a triple difference strategy? 

[Muralidharan and Prakash (2017)](https://www.aeaweb.org/articles?id=10.1257/app.20160004) run the following estimation equation in their Triple-Difference strategy: 

$$ y_{ihv} = \beta_0 + \beta_1 \times F_{ihv} \times T_{ihv} \times BH_{ihv} + \beta_2 \times F_{ihv} \times BH_{ihv} + \beta_3 \times T_{ihv} \times BH_{ihv} + \beta_4 \times F_{ihv} \times T_{ihv} + \beta_5 \times F_{ihv} + \beta_6 \times T_{ihv} + \beta_7 \times BH_{ihv} + \epsilon_{ihv} $$, 

with $$y_{ihv}$$ being the outcome variable of interest corresponding to child i in household h and village v and $$F_{ihv}$$ being an indicator for being female, $$T_{ihv}$$ being an indicator for being in a "treated" cohort (being aged 14 or 15), and $$BH_{ihv}$$ is an indicator for an observation from the state Bihar. As you can see from this equation, $$\beta_1$$ is the triple-difference estimator, and $$\beta_2$$ to $$\beta_4$$ are estimators for the double differences. $$\beta_5$$ to $$\beta_7$$ are estimators for linear trends. 

## Parallel trends: Logs or levels? 

...

