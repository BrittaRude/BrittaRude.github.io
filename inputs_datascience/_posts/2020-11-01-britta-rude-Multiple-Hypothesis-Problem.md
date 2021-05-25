---
layout: post
usemathjax: true 
title: "Multiple Hypothesis Testing"
date: 2020-11-02
image: /images/DataScience_FeatureImage.jpg
---

## Multiple Hypothesis Testing - Why should we care? 

Often we are interest in a variety of different outcomes when analyzing the effect of something. As an example, when estimating the effect of an early childhood developing program you might want to analyze its effect on a variety of learning outcomes, but also different health and nutrition indicators, or psychomotor performance measures, among others. The first that comes to mind when applying this in practice is to analyze the effect of the program on these outcomes of interest one by one. One chooses a significance level (e.g. 0.05) under which the hypothesis should be rejected. But the larger the number of hypothesis one is testing, the larger the probability that there is a significant effect only by chance. In the case of 20 different hypotheses, for example, this chance would be 64 percent (1-(1-0.05)^5) ([Goldman,2008](https://www.stat.berkeley.edu/~mgoldman/Section0402.pdf)). 

## Multiple Hypothesis Testing - What can we do? 

Statisticians have developed several methods to correct for this problem. One is the Bonferroni correction, which divides the significance level by the number of hypothesis. Another one is the False Discovery Rate (the proportion of false positives among all significant results). Another possibility is to apply the positive False Discovery Rate (fixing  the  rejection  region,  then  estimating the significance level). A recent [post](https://medium.com/idinsight-blog/more-outcomes-more-problems-a-practical-guide-to-multiple-hypothesis-testing-in-impact-98da5037b26) compares the different approaches and highlights when they might fit best. 

To summarize, the main approaches are as follows: 
- Bonferroni: The p-value is equal to the significance level divided by the number of hypothesis - The downside is that this increases the probability of rejecting a hypothesis when there is no effect
- FWER and sequential adjustments: the p-value is equal to the minimum of multiplying a decending sequence (starting from the number of hypotheses m) with the p-value of each respective hypothesis - This method is most appropriate when you want to guard against any form of false positive.  
- FDR and pFDR: Here you account for the false-discovery rate (FDR), which is the share of the number of times a null hypothesis is false characterized as significant over the total amount of rejected hypotheses. 

## Multiple Hypothesis Testing - How can we apply it? 

The World Bank has summarized how to address this empirically in a [blog post](https://blogs.worldbank.org/impactevaluations/overview-multiple-hypothesis-testing-commands-stata). 




