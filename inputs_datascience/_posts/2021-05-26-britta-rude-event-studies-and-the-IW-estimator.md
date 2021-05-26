---
layout: post
usemathjax: true 
title: "Turning Event Studies around - The IW Estimator"
date: 2021-05-26
image: /images/DataScience_FeatureImage.jpg
---

## The problems with event-studies 

Event studies are used often in economic research to analyze the effect of policies. The estimation strategy is based on the grouping of the data into different cohorts. Each cohort is defined based on the number of periods that have passed since a treatment. This is the case for minimum wages, for example. If the minimum wage is introduced in August 2017 in state A, one period has passed in September 2018, and -1 periods in July 2018. Assuming that a treatment is introduced at different points in time for different treatment units, this empirical strategy can help us to identify causal effects. If the minimum wage is introduced in December 2017 in State B, one period has passes in January 2018 and -1 in November 2017. 
