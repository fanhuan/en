---
layout: post
title: GBLUP Overfitting
categories: [notes]
tags: [stats]
---

A while ago we explained the math behind [BLUP](https://fanhuan.github.io/en/2025/02/19/BLUP/). Recently I was doing some GBLUP for a bunch of traits on the same individuals and for some traits, the accuracy of the prediction was greater than the broad-sense heritability (h2)! This should not be happening and this is to document my debugging process.

Firstly some background on BLUP vs GBLUP. Simply put, the model is exactly the same, y = Xb + Za + e. The difference lies in Z. In BLUP, it is usually the [A matrix](https://fanhuan.github.io/en/2024/10/14/A-Matrix/) which is based on pedigree; while in GBLUP, it is usually a genetic relationship matrix (GRM) calculated based on molecular markers such as SNPs. In my case it is a GRM calculated based on millions of WGS markers.   

The tool I was using is a R package called [rrblup](https://cran.r-project.org/web/packages/rrBLUP/index.html). Let's first define how I calculate h2 and the accuracy then why there might be overfitting. In this post we will not talk about fixed effect. Maybe in another post we will do. 

For h2, it is calculated with the full data:
```
model <- rrblup::mixed.solve(y=y, K=GRM)
h2 <- model$Vu/(model$Vu + model$Ve) 
```

For accuracy: `cor(y, model$u)^2`. Note that in `rrblup::mixed.solve()`, `model$u` is `Za`, not `a`. The corrent form should actually be `cor(Za + e, Za)^2`, but since in this post there is no fixed effect, this is equivalent to `cor(y, Za)^2`.

# Hypothesis 1: rescale of GRM. Did not help.

```
# Check if K is properly scaled
mean(diag(GRM))  # Should be close to 1
# If not, try:
G_scaled <- GRM / mean(diag(GRM))
model <- mixed.solve(y = y, K = G_scaled)
```

# Hypothesis 2: population structure can create "information leakage"

From the BLUP calculation, we know that the weight is much higher from their close relatives than others in the linear combination for the prediction. Therefore when there is strong population structure (which is true in my dataset), individual i from population A is predicted mainly by its close relatives. The genetic correlation (represented in their pairwise similarity in GRM) can be __confounded__ with __population-specific effects__, such as environmental factors or other cryptic relatedness. Since we only have one random effect term, everything is lumpped into this term. 

Now the real problem is, why this inflates r2, but not h2?



captures both __true genetic effects__ and This is because some of the  





