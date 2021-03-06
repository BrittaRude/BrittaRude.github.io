---
layout: post
usemathjax: true 
title: "Turning Event Studies around - The IW Estimator"
date: 2021-05-26
image: /images/DataScience_FeatureImage.jpg
---

## The problems with treatment heterogeneity in event-studies 

Event studies are used often in economic research to analyze the effect of policies. The estimation strategy is based on the grouping of the data into different cohorts. Each cohort is defined based on the number of periods that have passed since a treatment (a policy or an event) occured. This is the case for minimum wages, for example. If the minimum wage is introduced in August 2017 in state A, one period has passed in September 2018, and -1 periods in July 2018. Assuming that a treatment is introduced at different points in time for different treatment units, this empirical strategy can help us to identify causal effects. If the minimum wage is introduced in December 2017 in State B, one period has passed in January 2018 and -1 in November 2017. The interesting thing here now is that the data is organized in the sense that state A and B are grouped under period 1 and period -1 although the timing is different. 

A recent paper by [Sun and Abraham (2020)](http://economics.mit.edu/files/14964) points out important pitfalls with this empirial approach: 

- The coefficients on the relative time periods (e.g. 1) are linear combinations of its own relative periods, but also of the other relative periods (e.g. of -1). 

- This results in a contamination of each relative time periods' coefficient through the other relative periods if there is treatment heterogeneity. 

- The practice of using treatment leads as parallel pretrends is problematic in this case. 

- The underlying weights behind the linear combinations have no policy-implications and are difficult to interpret. 

- Using alternative weights, based on the cohort shares, results in estimators that are more robust to treatment effect heterogeneity. The new treatment effect is a weighted average of the underlying treatment effects called the IW estimator.

## But wait a minute - what do heterogeneous treatment effects actually mean? 

As [Sun and Abraham (2020)](http://economics.mit.edu/files/14964) correctly state, the assumption of treatment heterogeneity is quite a strong one to make. It means that each cohort experiences the same treatment effects path. That is, the treatment effect for state A in period 1 (subject to a minimum wage increase in August 2017) is the same as the one for State B in period 1 (subject to a minimum wage increase in December 2017). Dynamic treatment effects can arise when e.g. treatment effects differ by age and the age composition then differs by cohorts. Even when controlling for age the treatment affect may vary over time if invidiuals select into treatment timings depending on their age. Another example are macroeconomic conditions (e.g. fluctuations in the business cycle). 

## The problem of pre-trend testing 

This has huge implications for empirical work. Traditionally, economists have used pre-trends to establish if the identification assumption of parallel trends is satisfied. But Sun and Abraham's work has clearly shown how problematic this is (at least in the case of heterogeneous treatment effects). Good then that Sun an Abraham have already developed a new estimator that is robust to treatment effect heterogeneity - the IW estimator (interaction-weighted estimator). The estimator is basically a weighted average of treatment effects and draws from weights which are equal to cohort-shares. The estimator is consistent under parallel trends and no anticipation behavior. 

## A practical application 

### 3 different assumptions - do they apply? 

The authors illustrate their findings using a concrete example, namely the paper by [Dobkin et al. (2018)](https://economics.mit.edu/files/13738). In their paper the researchers study the economic effects of hospitalization, applying an event study with dynamic treatment effects (namely the effects on out-of-pocket medical spending and labor earnings). In the case of the paper there are 4 cohorts as individuals report being admitted to a hospital for 4 different points in time. As outlined by Abraham and Sun one should check 3 assumptions when implementing a Event-Study: 

- Parallel trends (Assumption 1): The parallel trend is unlikely to apply in this case, as individuals who end up in a hospital are likely to experience different health trends already before being admitted to a hospital when compared to those individuals not ending up in a hospital. This is why [Dobkin et al. (2018)](https://economics.mit.edu/files/13738) restrict the assumption to those individuals who were ever hospitalized. 

- No anticipation behavior (Assumption 2): As the treatment is restricted to unexpected hospitalization it is unlikely that individuals will be able to anticipate when they will end up in a hospital. 

- Treatment effect heterogeneity (Assumption 3): Abraham and Sun show that for out-of-pocket medical spending the effect of hospitalization is likely to be heterogeneous across individuals from different waves. This is due to the fact that those ending up in the hospital in later waves are automatically older. And age, on the other hand, is correlated with health insurance, which then again influences out-of-pocket medical spending. Similarly, individuals who ended up in hospitals during the financial crisis might find it more difficult to return to their job after being released than those being admitted after or before the crisis. 

### Heterogenous effects, hospitalization and the IW estimator

Sun and Abraham then show the implications of their findings through plotting the estimator on the second pre-treatmen period (period -2). Plotting the decomposition of this estimator shows that it is influenced by the lags of the treatment (periods 1-4). This means that the estimator on -2 actually does not isolate the pre-trends. They next estimate the IW estimator and show that the results differ slightly. 

## Putting it all to practice 

Sun and Abraham (2020) have developed a stata code with which you can plot the eventstudy weights called and assess the degree of contamination in your setup [eventstudyweights](https://ideas.repec.org/c/boc/bocode/s458833.html). The IW estimator can be implemented via the command [eventstudyinteract](https://github.com/lsun20/EventStudyInteract). 




