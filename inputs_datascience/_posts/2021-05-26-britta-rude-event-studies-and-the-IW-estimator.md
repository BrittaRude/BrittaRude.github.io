---
layout: post
usemathjax: true 
title: "Turning Event Studies around - The IW Estimator"
date: 2021-05-26
image: /images/DataScience_FeatureImage.jpg
---

## The problems with treatment heterogeneity in event-studies 

Event studies are used often in economic research to analyze the effect of policies. The estimation strategy is based on the grouping of the data into different cohorts. Each cohort is defined based on the number of periods that have passed since a treatment (a policy or an event) occured. This is the case for minimum wages, for example. If the minimum wage is introduced in August 2017 in state A, one period has passed in September 2018, and -1 periods in July 2018. Assuming that a treatment is introduced at different points in time for different treatment units, this empirical strategy can help us to identify causal effects. If the minimum wage is introduced in December 2017 in State B, one period has passed in January 2018 and -1 in November 2017. The interesting thing here now is that the data is organized in the sense that state A and B are grouped under period 1 and period -1 although the timing is different. 

A recent paper by [Sun and Abraham (2020)](http://economics.mit.edu/files/14964) points out important pitfall with this empirial approach: 

- The coefficients on the relative time periods (e.g. 1) are linear combinations of its own relative periods, but also of the other relative periods (e.g. of -1). 

- This results in a contamination of each relative time periods' coefficient through the other relative periods if there is treatment heterogeneity. 

- The practice of using treatment leads as parallel pretrends is problematic in this case. 

- The underlying weights behind the linear combinations have no policy-implications and are difficult to interpret. 

- Using alternative weights, based on the cohort shares, results in estimators that are more robust to treatment effect heterogeneity. The new treatment effect is a weighted average of the underlying treatment effects called the IW estimator.






