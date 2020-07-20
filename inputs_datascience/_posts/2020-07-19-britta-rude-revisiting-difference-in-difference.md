---
layout: post
title: "Revisiting Difference-in-Difference - Taking the DiD Design to a next level ?"
date: 2020-07-18
---

## Traditional Difference-in-Difference: What do we need it for? 

Often we do not have the money nor the time to conduct field experiments, especially in development countries. Therefore, economists often employ quasi-experimental strategies, taking advantage of so-called natural experiments. Image that you want to exploit the effect of deforestation on poverty. Instead of implementing a field experiment, in which you deforest randomply-selected treatment areas, and leave randomly-selected control areas untouched, you can search for natural experiments. Image that there has been a fire due to lack of rain and heat in a certain region, similar to what has happened in Australia, or also several parts in Latin America. You can simply take this event as a natural experiment, and a sort of "proxy" for deforestation. You can then compare the evolution of poverty over time in the by the disaster affected region to a region that has been unaffected by the event. 

This is what economists traditionally call a difference-in-difference design. It is a method that tries to solve the issue of missing counterfactuals. The problem with experiments in general is, that you observe each individual only once. If you try to find out the effect of a certain vaccine, you only observe person i with our without the vaccine. You do not observe this same individual in both states, that is, with and without the vaccine. Therefore, if this person i gets sick after receiving the vaccine, you cannot know if this person would have gotten sick anyways, even without participating in the experiment, or if s/he got sick due to that. 

In field experiments you try to solve this issue through two actions: Firstly, you choose a large enough sample to make sure that by coincidence you do not only have people with unknown, underlying health issues in your treatment group, that will all react strongly and badly to the vaccine, while only having super healthy people in your control group, or vice versa. Secondly, you randomly distribute your sample to treatment and control groups, to avoid that treatment and control groups differ on certain observable and unobservable characteristics. If you combine both actions, chances are large that you will get a sample that is representative for your underlying populatoin, leading you to a pretty reliable result of your vaccine's effect. 

Natural experiments try to mimique these field experiments. Let's have a closer look at the traditional difference-in-difference design, and let's go back to our wildfire example to better understand it. Analyzing poverty data over time in the region affected by the wildfires won't do the job to get a full picture, about what is happening. Why? Because we are missing a counterfactual. We are only observing the poverty evolution of people who have been affected by the wildfire. If we know observe that poverty increased for them, it is impossible for us to disentangle this effect from underlying poverty dynamics that would have developed in any case. Maybe the wildfires just arose in this region as its inhabitants were becoming poorer and poorer anyways, and therefore did invest less in protective gear. We cannot observe this, if we don't have a comparison group. 

Difference-in-Difference provides us with a solution for this problematic. What we do in Difference-in-Difference is to take a region that is similar to the region unaffected by the treatment we are interest in, in this case wildfires, and compare the evolution of poverty in our wildfire region (the treatment group) to the evolution of poverty in our control region (the control group). We can then subtract the difference in poverty over time observed in both regions from each other, and have an estimate that is a relatively good proxy for what we are actually looking for. 

<table align="center">
  <tr><th align="center"></th><th align="center">Before Treatment</th><th align="center">After Treatment</th><th align="center">Differences</th></tr>
  <tr><td>Treatment Group</td><td>A</td><td>B</td><td>A-B</td></tr>
  <tr><td>Control Group</td><td>C</td><td>D</td><td>C-D</td></tr>
  <tr><td>Differences</td><td>A-C</td><td>B-D</td><td>(A-B) - (C-D)</td></tr>
</table>

## Traditional Difference-in-Difference: When does it actually work, and what do we need to keep in mind? 

This research strategy only works under an important condition, known under the name "parallel trend" assumption. As discussed previously, it is important for our estimation effect that our treatment and control groups are similar to each other, and therefore comparable, that is, our control group is a good counterfactual for our treatment group. I can test this assumption graphically by plotting our outcome of interest over time and test if they evolve similarly to each other before the imposition of the treatment of interest, in our case, wildfires.  


