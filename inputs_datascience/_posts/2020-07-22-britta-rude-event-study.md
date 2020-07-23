---
layout: post
usemathjax: true 
title: "Event Studies - What do they show, and why do we need them?"
date: 2020-07-22
---

## Event Studies: What is an event study? 

Often in economic papers that use Difference-in-Difference, or the staggered version of it, we can observe that researchers conduct event studies as robustness tests. A recent working paper by [Jonathan Roth (2020)](https://scholar.harvard.edu/files/jroth/files/roth_pretrends_testing.pdf) reflects on this method, and the fact that economists often apply event-studies to test for pre-trends (or the famous parallel trend assumption). He finds that over 70 journals published in the AEA report an event-plot. He finds that the power of these tests is often low, and that distortions from pre-testing is a concern. 

But before getting on the details on this, let's revise what it actually means to conduct an event study. Therefore, let's have a look at the paper by [Lafortune et al.(2018)](https://pubs.aeaweb.org/doi/pdfplus/10.1257/app.20160567). They study the impact of post-1990 school finance reforms on student achievement in low-income school districts, using an event-study design and nationally representative data. The authors show that educational spending as measured by real per pupil revenues has constantly increased between 1990 and 2012. They argue that most of these increases are due to school finance reforms, directed at low-income districts. 

<img src="/images/Lafortune_2017_SFR.PNG" alt="Lafortune SFR" style="max-width:60%;"/>

They construct a state-by-year panel based on fourth- and eighth-graders taking a yearly math and and reading test. They employ an event study to distinguish the effects of the reforms from other potential determinants of these test-score cards. The event study suits their purpose as the reforms were implemented randomly across states and time. They state that there are 64 school-financed reform events in 26 states between 1990 and 2011. Of these 26 states, 18 had multiple events. The states that do introduce a reform in a certain year forms the treatment group in this set-up, while states without a reform form the control group, after accounting for fixed effects at the state and year level. Following this, the estimation equation looks like that: 

$$ \theta_{st} = \delta_s + \kappa_t + 1(t>t_s*) \beta_{jump} + \epsilon_{st} $$

They test for parallel trends before the implementation of the reforms and find that there are no systematic changes in the periods leading up to the reform. They find clear changes in student achievement after the reforms. Ten years after a reform, relative achievement of students in  low-income districts has risen by roughly 0.1 standard deviation. 




