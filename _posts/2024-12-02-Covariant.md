---
layout: post
title: Variable, Covariables and Covariates.
categories: [notes]
tags: [stats]
---



12.10 Partial RDA(redundancy analysis) and variance partitioning

Covariables, What they are and 

When you are not interested in the infuluence of specific explanatory variables, it is possible to partial out their effect using technics such as partial linear regression.

Chapter 5.3 Partial Linear Regression.

5.2 Multiple (linear) regression analysis: A multiple regression analysis is a statistical technique used to examine the relationship between one dependent variable (or outcome variable) and two or more independent variables (or predictors). 

Backwards selection:

Why do we do model selection? We know that we cannot include all the parameters. Some are not significant; some will not pass the test (the increase of parameter will be punished). Another way to think about it is that we should not use the full model. First of all we have the problem of overfitting. Why is overfitting a problem? It is too tailor to the current dataset thus it is very hard to use the model to predict for unseen data or future events. Another way to understand the necessity of using only a subset of the parameters is that (i) the interpretation of the model, the fewer the parameters the easier to interpretate. This is very similar to the predictability of models. (ii) precision of predicted intervals and confidence bands ill be smaller. This is because each parameter will have its own se. The more the paramters, the bigger the sd in the prediction. 

Therefore there are many ways of evaluating how optimal a model is for different subsets of the same set of parameters. 

 1. The very famouse AIC (Akaike Information Criteria). Who is Akaike? Hirotugu Akaike (赤池 弘次) is a japanese statistician. The formular looks like this:

 AIC = n log(SSresidual) + 2(p+1) -nlog(n). 

 As you can see there are thre parameters, n(# of observations), SSresidual (goodness of fit) and p(# of parameters). In model selectin, n is fixed, therefore we are basically comparing how well the model fits the data while punishing the number of parameters. As you can see if the scale of y is very big, the effect of p will be relatively small even after log. 

 2. Adjusted R2. Usually R2 = 1- SS_residual/SS_total. Adjusted R2 panelize for parameters.

 Adjusted R2 = 1 - SS_residual/SS_total * (n-1)/(n-1-p). Why (n-1)/(n-p+1)?

  

 3. BIC (Bayesian Information Criterion). Formular:

 BIC = -2ln(L) + pln(n)




