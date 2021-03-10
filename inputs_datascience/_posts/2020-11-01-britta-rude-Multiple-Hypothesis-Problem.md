---
layout: post
usemathjax: true 
title: "Multiple Hypothesis Testing"
date: 2020-11-02
image: /images/DataScience_FeatureImage.jpg
---

## Multiple Hypothesis Testing - Why should we care? 

Often when you are analyzing something you are interest in a variety of different outcomes. As an example, when analyzing the effect of an early childhood developing program you might want to analyze its effect on a variety of learning outcomes, but also different health and nutrition indicators, psychomotor performance measures, among others. The first that comes to mind is to analyze the effect of the program on these outcomes of interest one by one. One than chooses a significance level (e.g. 0.5 percent). But the larger the number of hypothesis one is testing, the larger the probability that there is a significant effect only by chance. In the case of 20 different hypotheses, for example, this chance would be 64 percent (1-(1-0.05)^5) ([Goldman,2008](https://www.stat.berkeley.edu/~mgoldman/Section0402.pdf)). 


