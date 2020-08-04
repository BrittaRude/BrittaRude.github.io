---
layout: post
usemathjax: true 
title: "What is all this modeling in economics about?"
date: 2020-08-03
---

## Economic models - What do we need them for? 

Economic models are frameworks that can help us to structure underlying problems. They help us to simplify complex phenomenon observed in real life, to better understand them. This simplification is based on a number of assumptions we make about real life (as for example, that individuals forming part of an economy have perfect information). Often, these models are based on mathematical frameworks and include endogenoues as well as exogenous variables. Endogenous variables are determined by other variables forming part of the models, while exogeneous variables are determined outside of the system. For example, if I analyze the effect of the minimum wage on labor supply, and construct an economic model to better understand it, labor supply is my endogeneous variable while the minimum wage is determined exogeneously. 

Let's have a look at a concrete example to further understand the importance of economic models for economics. Let's assume that we would like to analyze return migration of Mexican migrants in the US, as in the paper by [Carmen E. Carrión-Flores (2018)](https://link.springer.com/article/10.1186/s40176-017-0108-0). She asks the question about optimal migration duration of Mexican immigrants in the USA using individual-level data from the Mexican Migration Project (MMP). She uses the Cox proportional hazard model to examine the determinants of return migration from the US to Mexico. The author looks at several drivers of return migration: The costs of migration measured by the distances from origin states in Mexico to destination states in the USA, the  US expected wage as measured by a function of both the unemployment rate level and the average US real wage at the state level, as well as US migration policies. She also looks at potential determinents that have been studies in past research: Demographic characteristics, individual characteristics as language ability, economic conditions, and social networks. The author examines if the identified drivers of migration duration differ by the number of migrant trips. 

A large economic literature shows that migration decisions are driven by wage differentials. Individuals move if the discounted income stream between two destinations exceeds moving costs (as in the paper by [Sjaastad (1962)](https://www.jstor.org/stable/1829105?seq=1]). There are two factors that influence this migration decision: Social networks, and local labor market conditions, especially the demand for low-skilled labor in the US. But what about the duration of migrants' stay in the US? Why do they return despite a positive wage differential? Past research has tried to explain this through consumption preferences by migrants to consume at home (see for example [Dustmann & Weiss (2006)](https://www.ucl.ac.uk/~uctpb21/Cpapers/optimalmigrationduration.pdf)), due to for example their family livign at home, or the existence of a higher purchasing power of the dollar in Mexico (as in [Dustmann (2003)](https://www.sciencedirect.com/science/article/pii/S0014292101001842). Another paper by [Angelucci (2012)](https://www.jstor.org/stable/10.1086/662575?seq=1) addresses the role of border enforcement policies. 

## The Cox proportional hazard model and optimal migration duration 

The Cox proportional hazard models lies upon the following assumption: 

- The economy starts at an initial point t=0, during which the migrant can choose between migrating or staying. People die in period T.  

- The migrant bases her decision on a positive wage differential ($$ \omega_{USA} > \omega_{MEX} $$, and there is no uncertainty about wages. 

- Wages in the US and Mexico are constant along the lifecycle of the migrant, but migrants have different abilities and migrant costs.  

- The migrant is interested in consumption in both countries ($$ c_{USA}$$ and $$c_{MEX} $$), and his utility function depends upon it. 

- The migrants decides about returning to Mexico every month, depending on her optimal migration duration $$ t \in (t, T) $$. The optimal migration duration is associated with her ability. 

- If $$t = 0$$ or $$t=T$$ the migrant is a permament stayer. The model abstracts from this, and only considers interior solution, that is, temporary migration. 

- The model abstracts from savings, as it is assumed that the migrants consumes all her savings to cover the moving costs. 

- The migrants choice function is: $$ \sum_{\tau=1}^t u(\mu^{us}, c^{us}) + \sum_{\tau=1+t}^T(\mu^{mex}, c^{mex})$$. With the assumption of constant wages, the utility function is: $$ t u(\mu^{us}, c^{us}) + (T-t) u(\mu^{mex}, c^{mex})$$. The utility function has diminishing marginal returns, and $$\mu^{mex} and \mu^{us} $$ are preference parameters for consumption at home or in the migration destination. If $$\mu^{mex} > \mu^{us} $$, the migrant prefers to consume at home. We assume that both preference parameters are larger than 0. 

The maximization problem then is as follows: 

$$ max_{c^{us}, c^{mex}, t \in (t,T)} U(c^{us}, c^{mex}, t) =  t u(\mu^{us}, c^{us}) + (T-t) u(\mu^{mex}, c^{mex})$$

subject to the following budget constraint: 

a) $$(T-t) \omega^{mex} + t \omega^{us} - t pc^{us} - (T-t) c^{mex} - cc = 0$$

b) $$\omega^{mex} < \omega^{us}$$

p is the relative price of consumping in the US, with $$p>1$$ indicating that consumption in the US is more costly than in Mexico. cc represents the cost of moving (transportation costs, ability to cross the border, non-labor income (e.g., income from property owned in Mexico), and forgone income). 

When solving the Lagrange equation with respect to the optimal duration t we get to the following solution, with $$\theta$$ being the marginal utility of wealth: 

$$\theta[(\omega^{us} - pc^{us}) - (\omega^{mex} - c^{mex})] - [u(\mu^{mex}, c^{mex}) - u(\mu^{us}, c^{us})] = 0$$

$$\theta[(\omega^{us} - pc^{us}) - (\omega^{mex} - c^{mex})]$$ is the benefit of an additional month in the US. The term is positive under the following conditions: $$\theta[(\omega^{us} - \omega^{mex}) > (pc^{us} - c^{mex})]$$, $$\omega^{us} > \omega^{mex}$$, and $$p>1$$. The term is decreasing in t. The second term is the cost of staying an additional month in the US and is increasing in t. The optimal duration time is achieved when the first term is equal to the second term, that is, the benefit of staying an additional month in the US is equal to the cost of staying. 

## How does optimal migration duration react to varying exogeneous factors? 

This is what economists call comparative statistics. Looking at first-order derivates, they analyze how a variable of interest reacts to the change in other variables, through looking at the sign of the derivate (is it positive, or negative?). [Carmen E. Carrión-Flores (2018)](https://link.springer.com/article/10.1186/s40176-017-0108-0) does the same in her paper. 

She finds that: $$\delta t = \psi_1 d \omega^{mex} + \psi_2 d \omega^{us}$$, with $$\Psi_i$$ being partial derivatives with respect to $$\theta, t, \omega^{mex} and \omega^{us}$$. 

As indicated above the current literature suggests an increase in the optimal duration if the wage differential increases. Contrary, [Carmen E. Carrión-Flores (2018)](https://link.springer.com/article/10.1186/s40176-017-0108-0) finds in the application of her model that the income effect is negative as the value of staying in the US one additional month decreases as total wealth increases, leading to a reduction in the optimal migration duration. The model therefore predicts that an increase in the wage differential could lead to an increase or decrease of migration duration. On the on hand, migrants prefer to spend as little time as possible away from their home country, and during this time, earn as much as possible. This leads to a decrease of duration time after an increase in the wage differential. On the other hand, lower wages might make it more difficult to cover their cost of migrating, therefore leading to the opposite effect. Also, if migrant costs increase, the migrant is expected to stay for a longer time period. The author leaves the reaction of optimal migration duration to alternative factors, as for example an increase in the real wages in Mexico, an increase in border enforcement, and an increase in migration experience, to further research. 

## The Cox proportional hazard model as a mirror of reality? 

The example above has shown how theoretical models can help us to better understand and structure real-life economic models, and make predictions about reality. But relying on these theoretical models is often not enough. This is why economists often try to validate these theoretical models in real life, and test if the predictions made hold with real data. [Carmen E. Carrión-Flores (2018)](https://link.springer.com/article/10.1186/s40176-017-0108-0) uses data from the Mexian Migration Project (MMP) to validate the Cox proportional hazard model. She focuses on migrants between 15 and 64 years old, who undertook migration trips to the US between 1963 and 1999. She creates two samples, the first one being 2375 "first migration trips", and the second one being 2658 "last migration trips". She includes several control variables: 

- For individual characteristics: Gender, age, marital status, number of children

- For ability: Education, occupation, English proficiency

- For migration costs: Urban versus rural, apprehension rate, and distance in miles

- Social networks: Kins in the US, social networks, expected wage

- US migration policies: IRCA (1986), IA (1990), and IIRA (1996)

- Long-term savings: Properties in Mexico, long-term savings, and remittances

Her main explanatory variable of interested is the expected wage, that depends on the mean wage ($$w_{it}$$), the CPI in the US ($$P_{it}$$) and the propensity to find a job: ($$u_{it}$$): $$(1- u_{it})(w_{it} / p_{it})$$. A downside of this wage measure is that it only varies at the state level, and does not account for differences across the skill distribution of migrants. A further downside is that the expected wage differential could be confounded by different wage inequality across US states. [Carmen E. Carrión-Flores (2018)](https://link.springer.com/article/10.1186/s40176-017-0108-0) tries to account for this by controling for sector of employment. The main outcome variable is the migration duration measured in months. It is important to note that her sample includes right-censoring (migrants who are still in the US and have not (yet) returned), but no left-censoring (migrants who are already in the US at the start of the sample). 

[Carmen E. Carrión-Flores (2018)](https://link.springer.com/article/10.1186/s40176-017-0108-0) estimates the hazard rate of migration duration as followed: 

$$ h(t; x_i) = h_0(t) \times exp(x_i' \beta)$$, 

with $$h_0(t)$$ being considered the baseline hazard rate, and $$ h(t; x_i)$$ being the hazard of return migration at time t for a migrant characterized by the vector $$/beta$$. The assumption of the hazard model is that the effect of the covariates is proportional to the baseline. The baseline hazard rate is the same for all individuals, and only the level of the hazard function differs by $$exp(x_i' \beta)$$ across individuals. Importantly, the Cox proportional hazard model does not make parametric assumptions on the underlying baseline hazard function.




























