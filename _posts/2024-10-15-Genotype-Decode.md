---
layout: post
title: Genotype Decode
categories: [notes]
tags: [GWAS]
---



As you have noticed there are many ways of denoting the genotypes even though it is usually a pretty straight forward thing for diploids: R/R, R/A and A/A.

1. VCF original: 0/0: R/R; 0/1: R/A; 1/1: A/A. ./.: missing call
2. Dosage of ALT: 0/1/2/NA. 
    1. Rule: 0/0: 0; 0/1: 1; 1/1: 2; ./.: NA. Note that heterozygotes are always represented as 0/1, no 1/0 would be called.
    2. Tools: can use `plink -recode A` to turn the vcf format or .bed into this format.
3. {-1,0,1}
    1. Rule: I am not sure about this one. I came across that this should be the dosage of major allele, not ref allele. But currently I am using it as if -1: R/R; 0: R/A; 1: A/A.
    2. Tools: both {sommer} and {rrBLUP} uses this format. Or to be more accurate, they can deal with any scaled (mean = 0) SNP matrix. 
4. 0/1. I actually do not know what this means. Homo and Hetero?

There are also tools that do not care about the format, e.g {BGLR}. You can either give it in the `0/1/2` format or `-1/0/1` format. You just need to scale it before using so that each column has a mean of 0 and a standard deviation of 1. This ensures that all SNPs are on a comparable scale. This standardization helps ensure that each SNP contributes equally to the model, which is particularly important when you have variables with different ranges (like 0, 1, 2 in the SNP matrix). Without scaling, SNPs with larger variance could disproportionately influence the model compared to those with smaller variance. In Bayesian ridge regression, this step ensures that the prior regularization is applied more evenly across all SNPs. 

If you are going to calculate h2 based on the Vu, you also need to further scale it by the number of SNPs so the random effect takes the appropriate portion of the total variance via `scale(X)/sqrt(ncol(X))`. y also need to be standardized in order to calculate h2 so it is camparable across traits. A matrix also need to be standadized, can do A/mean(diag(A)).

For methods that involves regularization (ridge regression, lasso, etc.), scaling also helps maintain stability and interpretability of the posterior distributions for the effect sizes of the SNPs. Here `regularization` means ways to favor flatting estimates therefore to prevent from overfitting. Ridge regression is usually referred to as `L2 regularization` and lasso is referred to as `L1 regularization`. The YouTube channel `StatQuest with Josh Starmer` has exellent vidoes on this topic that you can check out if you are not familiar with them yet.