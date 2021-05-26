---
layout: post
usemathjax: true 
title: "The famous Bacon Decomposition"
date: 2021-05-26
image: /images/DataScience_FeatureImage.jpg
---

## Let's talk Bacon!  

Recently, when presenting on conferences, people often tell you to have a look at the Bacon Decomposition. But what exactly is that? You can find the NBER Working Paper ([here](https://www.nber.org/papers/w25018)). The main message of the paper is that the estimator resulting from a staggered difference-in-difference (see [this earlier blog-post](https://brittarude.github.io/blog/2020/07/21/britta-rude-staggered-difference-in-difference-as-the-next-level) if you wonder what it is about) is a combination of all the different treatment and control combinations in the data. Specifically, it is a combination of the following 4 effects: 

- Early Group versus Untreated Group 

- Late Group versus Untreated Group

- Early Group versus Late Group (before the treatment occurs) 

- Late Group versus Early Group (after the treatment occurs)

The important insight from this is that every group acts as a control group at some point in time, and that the estimates might be biased if the treatment effects vary over time. The parallel trend assumption has to be fulfilled not only before the treatment occurs, but also after it occurs. Now you might ask yourself which weight each of these 4 subestimators receive. The weights depend on the variance and sample sizes. This means that a group with a larger sample size will play a larger role in the overall estimate than a group with a lower number of observations. The same applies to a group with a larger variance. Knowing this, we can then analyze where our overall DiD estimator comes from. Are there certain groups in our data which drive the overall estimate? And how heterogene are the different estimates observed in the data? It also means that the staggered Difference-in-Difference estimator only recovers the average effect if the treatment effect is homogenous over time.

## Bacon and policy-making  

Let's have a look at a more concrete example. Take a child-care policy that was rolled out on a monthly basis between May 2006 and May 2007 in 12 different states. Each month the policy is rolled out in a different state. In May 2006, state A is the treatmen group and states B-L are control groups. The parallel trend assumption has to be fulfilled for all periods before May 2006. In June 2006, state B is the treatment gorup and state C-L are control groups. But state A is also a treatment group for state B, as its treatment status does also not change in June 2006. If the treatment effect was to grow over time, this would bias the estimator and violate the parallel trend assumption. 

## Applying the bacon decomposition in practice

So, what can you do if you want to use staggered difference-in-difference in your paper? Luckily, there is a stat command that will help you to implement your Bacon-Decomposition easily. It is called "bacondecomp" and you can find more information [here](https://ideas.repec.org/c/boc/bocode/s458676.html). The command will show you a scatter plot of 2x2 difference-in-difference estimates and their associated weights. It also reports a reweighted treatment effect, accouting for all of the above. 






