---
layout: post
title: "How can we turn data science into a tool against inequality?"
date: 2020-07-18
---

## Staggering Difference-in-Difference - Taking the DiD Design to a next level 

### Traditional Difference-in-Difference: What do we need it for? 

Often we do not have the money nor the time to conduct field experiments, especially in development countries. Therefore, economists often employ quasi-experimental strategies, taking advantage of so-called natural experiments. Image that you want to exploit the effect of deforestation on poverty. Instead of implementing a field experiment, in which you deforest randomply-selected treatment areas, and leave randomly-selected control areas untouched, you can search for natural experiments. Image that there has been a fire due to lack of rain and heat in a certain region, similar to what has happened in Australia, or also several parts in Latin America. You can simply take this event as a natural experiment, and a sort of "proxy" for deforestation. You can then compare the evolution of poverty over time in the by the disaster affected region to a region that has been unaffected by the event. 

This is what economists traditionally call a difference-in-difference design. It is a method that tries to solve the issue of missing counterfactuals. The problem with experiments in general is, that you observe each individual only once. If you try to find out the effect of a certain vaccine, you only observe person i with our without the vaccine. You do not observe this same individual in both states, that is, with and without the vaccine. Therefore, if this person i gets sick after receiving the vaccine, you cannot know if this person would have gotten sick anyways, even without participating in the experiment, or if s/he got sick due to that. 

In field experiments you try to solve this issue through two actions: Firstly, you choose a large enough sample to make sure that by coincidence you do not only have people with unknown, underlying health issues in your treatment group, that will all react strongly and badly to the vaccine, while only having super healthy people in your control group, or vice versa. Secondly, you randomly distribute your sample to treatment and control groups, to avoid that treatment and control groups differ on certain observable and unobservable characteristics. If you combine both actions, chances are large that you will get a sample that is representative for your underlying populatoin, leading you to a pretty reliable result of your vaccine's effect. 

Natural experiments try to mimique these field experiments. Let's have a closer look at the traditional difference-in-difference design. 


