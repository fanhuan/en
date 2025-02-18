---
layout: post
title: Heritability and How To Estimate
categories: [notes]
tags: [GWAS]
---



In this post I am going to talk about what is heritability and how to estimate it.

# Simple linear regression

Previously, heritability of trait can be estimated using simple linear regression between expected genetic relationship and phenotypic correlation. Below is an example of human height from [Visscher 2010 ](https://doi.org/10.1017/S0016672310000571).

![img](https://fanhuan.github.io/en/images/Visser2010.png)

# Linear Mixed Model.

Another way is to use a kinship matrix(__K__) as the covariance structure for the phenotype observed. The intuition is that the higher the relatedness of two individuals, the higher their phenotypes are correlated under genetic control. Note that K is used as the covariance matrix in the random effect part of a linear mixed model. In ecological models, we won't be actively *predicting* random effects. Yes there will be coefficients calculated, but they are self-constrained in the sense that they follow normal distribution with a mean of 0. Its variance is what we actually care, not specific coefficients. Therefore these were somewhat seen as nuisance parameters. Here nuisance means that those are not of primary interest but still affect the model and its estimates.

In the pre-genomics era, A matrix are usually used as K. It is based on pedigree and you can read more [here](http://fanhuan.github.io/en/2024/10/14/A-Matrix/).

Now that we have genomic data, some sort of genetic similarity matrix is used instead of A matrix, such as [GRM](http://fanhuan.github.io/en/2024/12/12/GRM/). The latter is more accurate because it is a realized relationship instead of an expected relation based on pedigree.

Then it is like how you solve any linear mixed model, usually `REML` is used for example in `gcta --reml`. h2 is basically a ratio of variance terms `Vg/(Vg + Ve)`, therefore it is very important to standardize K, so that it has a mean of 0 and a mean of diagonal value of 1. Note that in Yang 2010 NG, the GRM generated via `gcta --make-grm`, the mean of diagonal value is still 1 but the mean of offdiagonal values are set to be 0, which means the mean of the whole matrix is slightly higher than 0.

# Reference

1. Population Genomics, Concepts, Approaches and Applications. (Springer Cham, 2019). doi:10.1007/978-3-030-04589-0.
2. Textbook Animal Breeding and Genetics (second edition, 2024) https://wiki.groenkennisnet.nl/space/TAB




