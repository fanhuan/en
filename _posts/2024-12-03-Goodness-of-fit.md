---
layout: post
title: Goodness of Fit
categories: [notes]
tags: [stats]
---


We keep hearing about this phrase, goodness of fit, sometimes hyphenated. But I never pause to think about that it is. I just search my papers, where I have several (close to 10!) statistical ebooks and none of them mentioned anything about it. Oh well. [Wiki it is](https://en.wikipedia.org/wiki/Goodness_of_fit). Btw what would you do? ChatGPT? I wish I could fix the comment part of my blog...

OK, here is what Wiki says about goodness of fit.

"The goodness of fit of a statistical model describes how well it fits a set of observations. Measures of goodness of fit typically __summarize the discrepancy between observed values and the values expected under the model in question__. Such measures can be used in statistical hypothesis testing, e.g. to test for normality of residuals, to test whether two samples are drawn from identical distributions (see Kolmogorov–Smirnov test), or whether outcome frequencies follow a specified distribution (see Pearson's chi-square test). In the analysis of variance, one of the components into which the variance is partitioned may be a lack-of-fit sum of squares."

So to paraphrase, the goodness of fit is a way to evaluate statistical models, and it focuses on how well the model (expectations) fits the observations. For example, R2 is a goodness-of-fit measure. This led me to think what other ways of evaluating statistical models could be. Recalling the steps we take after constructing a linear model, there are diagnostic tests (residual checks), model comparison, significance of coefficients, etc. Here is a summary table from ChatGPT:

![img](https://fanhuan.github.io/en/model_evaluation.png) 

However, as you can see, nothing was mentioned about the significance of coefficients. When I ask chatGPT, it says: "Testing whether a coefficient in a linear regression model is significant is not typically classified as a type of model evaluation. Instead, it is considered part of inference or hypothesis testing about the relationships between variables in the model." Oh my. Inference.

# Kyle's understand of inference.

In [Kyle's](https://communityecologyconservation.com/people/) advanced statistics [course](https://en.xtbg.ac.cn/at/cs/202405/t20240521_663204.html) where I co-teach, he did mention about inference and back then I did pause to contemplate on this word. On the slide for __Inference__ he says: 

1. How to evaluate whether our model fits the data well? This includes goodness-of-fit measure such as R2 and diagnostic tests that evaluates residuals.
2. How to evaluate whether all our predictors are useful for the model? This includes t-tests or ANOVA that evaluates model parameters. This is usually referred to as hypothesis testing, where we assumes the 
3.  

# Wiki's inference

When I looked on Wiki, [statistical inference](https://en.wikipedia.org/wiki/Statistical_inference) is mentioned as opposed to [descriptive statistics](https://en.wikipedia.org/wiki/Descriptive_statistics), which mainly includes "measures of central tendency and measures of variability or dispersion. Measures of central tendency include the mean, median and mode, while measures of variability include the standard deviation (or variance), the minimum and maximum values of the variables, kurtosis and skewness". 

"Statistical inference is the process of using data analysis to infer properties of an underlying probability distribution.[1] Inferential statistical analysis infers properties of a population, for example by testing hypotheses and deriving estimates. It is assumed that the observed data set is sampled from a larger population. "

Before we can evaluate whether the predictors are useful for the model, we need to first find/solve the parameters/coefficients. How parameters are found in models? I can think of four:

1. LSE: least squares estimation
2. MLE: maximum likelihood estimation
3. Bayesian: summarizing the posterior
4. Loss function: machine learning.

Then I asked ChatGPT to give me a more comprehensive table:

![img](https://fanhuan.github.io/en/parameter_estimation.png)







