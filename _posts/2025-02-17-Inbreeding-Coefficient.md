---
layout: post
title: Inbreeding Coefficient
categories: [notes]
tags: [ML]
---

# Inbreeding coefficient

The inbreeding coefficient is usually referred to as __F__. As we explained in the [IBD vs. IBS]() post, F is actually the probability of identity-by-decent (IBD) of two alleles. If the two alleles are in the same diploid individual, then F is the inbreeding coefficient of the individual at this locus.  



# Inbreeding coefficient at locus level

It is defined as `1 - O(f(Aa))/E(f(Aa))`. The expectation is based on Hardy Weinburg Equilibrium. It can be generalized to multi-allelic and polyploidy. See more in [this post](http://fanhuan.github.io/en/2024/12/02/HWE/). 

# Inbreeding coefficient at individual level

## PLINK 

But as you can see in the output of `plink --het`, each sample gets a __F__. It used `O(HOM)`, the number of observed homozygous loci (3rd column), and `E(HOM)`, the number of expected homozygous loci (4th column), and the total number of loci in the 5th column, `N(NM)`, to calculate F, the 5th column using equation `(O(HOM) - E(HOM)) / (N(NM) - E(HOM))`. The higher the F, the more inbreed, or more homozygous than expected. `O(HOM)` and `N(NM)` is easy to count. For a locus with MAF of `p`, its `E(HOM)` would be `1-2p(1-p)` based on HWE, then we just sum the `E(HOM)` up for all the loci where this individual has a genotype, or no missing data. If there is no missing data, the `E(HOM)` should be the same for all the individuals. 

## GRM

I was using `gcta --make-grm` and the values on the diagnols are supposed to be __1+F__. However I find huge discrepency between this value and the __F__ reported in `plink --het` and I was wondering why. I tried to [follow the Methods](http://fanhuan.github.io/en/2024/12/12/GRM/) on a toy dataset as described in Yang 2011 NG, and I understand they are doing very different things, but the general trend should be the same given the same dataset. 


