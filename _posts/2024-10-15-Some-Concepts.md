---
layout: post
title: Variance, Covariance, Correlation, Variation and Covariation
categories: [notes]
tags: [stats]
---


__Variance__ is the most basic one. It measures the spread or dispersion of a __single variable__’s values around its mean. Var(X)=1/n∑​(xi​−mean)^2. Standard deviation is sqrt(Var(X)).

There is concept called the __coefficient of variation__. It is calculated as the ratio of the standard deviation to the mean. and often expressed as a percentage: `CV=SD/Mean×100%`. Therefore it can be considered as a standardized form of dispersion.

__Covariance__ measures the degree to which __two variables__ change together. It indicates whether two variables tend to increase or decrease in tandem. Cov(X,Y)=1/n∑​(xi​−xmean)(yi​−mean​). You can see that if Xi and Yi are both greater or smaller than their means, the product will be positive. If the trend is different, it would be negative and the Cov(X,Y) will be smaller. 

Now that we already know variance and covariance, now we can introduce the variance of a vector (__multiple variables__), which is actually a __matrix__. Let's still consider the case with two variables, its variance will look like:  
[ Var(X)    Cov(X,Y) ]
[ Cov(X,Y)    Var(Y) ]

If there are n variables, it will be a n by n matrix where the variances of each variable is on the diagonal, and the off-diagonals are the covaraince of the i-th and j-th variables in the vector. 

__Correlation__ is __standardized covariance__. folumar of correlation is cor(y1, y2) = cov(y1, y2)/sqrt(var(y1)var(y2)). If the variables (y1, and y2) are already normalized (mean = 0, sd=1), then cor(y1, y2) = cov(y1, y2). Note that in simple linear regression: R2=cor(y, y_hat)^2. If you have a vcv (variance covariance) matrix, you can turn it into a correlation matrix via `stats::cov2cor`.

Both __variation and __covariation__ are broader terms comparing to __variance__ and __covariance__, which are precise statistic terms with defined calculation equations.

Another set of somewhat related concepts are __Variables__, __Covariables__ and __Covariates__. 

__Variable__ is a general term used to describe any characteristic, measurement, or attribute that can take on different values such as age, height, weight, blood pressure, genotype, etc. It is used in almost every context in statistics and data analysis.

__Covariate__ is a __variable__ that is possibly predictive of the outcome and is included in a model to adjust or control for its effect. In mixed models, it is always used as one of the fixed effect term. It is the one that you want to control for but not actually care about (sounds like random effect, right?!). For example, in a study examining the effect of a new drug on blood pressure, age and baseline blood pressure might be treated as covariates. Nota that this term sometimes can insinuate that this variable is categorical (or am I hallucinating?). For example in `gcta`, if your covariate is categorical, you use `--covar`. However if it is continuous, it is `--qcovar`, `q` for quantitative. 

__Covariable__ is synonymous with __Covariate__ but less common. 