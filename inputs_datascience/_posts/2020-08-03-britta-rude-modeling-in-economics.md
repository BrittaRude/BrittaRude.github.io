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

## The Cox proportional hazard model

The Cox proportional hazard models lies upon the following assumption: 

- The economy starts at an initial point t=0, during which the migrant can choose between migrating or staying. People die in period T.  

- The migrant bases her decision on a positive wage differential ($$ \omega_USA > \omega_MEX $$, and there is no uncertainty about wages. 

- Wages in the US and Mexico are constant along the lifecycle of the migrant, but migrants have different abilities and migrant costs.  

- The migrant is interested in consumption in both countries ($$ c_USA and c_MEX $$), and his utility function depends upon it. 

- The migrants decides about returning to Mexico every month, depending on her optimal migration duration $$ t \in (t, T) $$. The optimal migration duration is associated with her ability. 

- If $$t = 0$$ or $$t=T$$ the migrant is a permament stayer. The model abstracts from this, and only considers interior solution, that is, temporary migration. 

- The migrants choice function is: $$ \sum_{\tau=1}^t u(\mu^{us}, c^{us}) + \sum_{\tau=1+t}^T(\mu^{mex}, c^{mex})$$. With the assumption of constant wages, the utility function is: $$ t u(\mu^{us}, c^{us}) + (T-t) u(\mu^{mex}, c^{mex})$$. The utility function has diminishing marginal returns, and $$\mu^{mex} and \mu^{us} $$ are preference parameters for consumption at home or in the migration destination. If $$\mu^{mex} > \mu^{us} $$, the migrant prefers to consume at home. We assume that both preference parameters are larger than 0. 

The maximization problem then is as follows: 



















