---
layout: post
title: Variance, Covariance, Correlation, Variation and Covariation
categories: [notes]
tags: [GWAS][stats]
---




Variance is the most basic one. It measures the spread or dispersion of a __single variable__’s values around its mean. Var(X)=1/n∑​(xi​−mean)^2. Standard deviation is sqrt(Var(X)).

Covariance measures the degree to which two variables change together. It indicates whether two variables tend to increase or decrease in tandem. Cov(X,Y)=1/n∑​(xi​−xmean)(yi​−mean​). You can see that if Xi and Yi are both greater or smaller than their means, the product will be positive. If the trend is different, it would be negative and the Cov(X,Y) will be smaller. 

Correlation is standardized covariance. folumar of correlation is cor(y1, y2) = cov(y1, y2)/sqrt(var(y1)var(y2)). If the variables (y1, and y2) are already normalized (mean = 0, sd=1), then cor(y1, y2) = cov(y1, y2)

Both variation and covariation are broader terms comparing to variance and covariance, which are precise statistic terms with defined calculation equations.
