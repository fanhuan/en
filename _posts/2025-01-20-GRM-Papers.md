---
layout: post
title: GRM for Family Data
categories: [notes]
tags: [GWAS]
---

## 0: Unrelated individuals

In a [previous post](https://fanhuan.github.io/en/2024/12/12/GRM/) we talked about how GRM is calcuated in Proferssor Yang Jian's landmark [2010 NG paper](https://www.nature.com/articles/ng.608). It is for unrelated individuals, where it is assumed that the average relationship between all pairs of individuals in 0 and the average relationships of an individual with him- or herself is 1 (see the last paragraph of the __Statistical framework__ of the __ONLINE METHODS__ section). There is a slightly differen flavor for this version 


> --make-grm-alg 0
> The default value is 0, and the GRM is calculated using the equation sum{[(xij - 2pi)*(xik - 2pi)] / [2pi(1-pi)]} as described in Yang et al. 2010 Nat Genet. If the value = 1, the GRM will be calculated using the equation sum[(xij - 2pi)(xik - 2pi)] / sum[2pi(1-pi)].

For my data it does not make a big difference.

## 1: Inbred data

On the [same page](https://yanglab.westlake.edu.cn/software/gcta/#MakingaGRM) as the usual `--make-grm`, there is an option called `--make-grm-inbred Make a GRM for an inbred population such as inbred mice or inbred crops.` Note the difference between inbred data and family data. Inbred data is usually referring to low, very low degree of heterozygosity, which is usually a result of generations of inbred. Whereas family data still have a good amount of heterozygosity. This definition focus on the pedigree. In the Citation part, two papers were mentioned, Yang 2010 NG and Yang 2011 AJHG (the GCTA paper). However when I searched for the word `inbred` in both papers, no hits. Therefore theoretically, I do not know what happens when you use this option. However, I did compapred the two GRMs resulted from the two options with the same input data, let's call them `GRM` and `GRM_inbred`, and at least for my data, `GRM` = `GRM_inbred` * 2. 


## 2: Family data.

GCTA offers an [implementation](https://yanglab.westlake.edu.cn/software/gcta/#GREMLinfamilydata) of this method proposed by [Zaitlen et al. 2013 PLoS Genetics](https://doi.org/10.1371/journal.pgen.1003520). This is their description of this method: 


> ...estimate pedigree-based and SNP-based h2 simultaneously in one model using family data. The main advantage of this method is that it allows us to estimate SNP-based h2 in family data without having to remove related individuals. 

Their documentation is great. Basically you make a grm with `--make-grm`, and then you create another grm based on this grm using `--make-bK`. This sets the off-diagnals (relationships) that are lower than user-defined threshold (i.e. unrelated) to 0. Then this GRM is about related individuals only. You can inclulde both matrices in your model as random effect if you have a mixed of unrelated (the first GRM) and related (the second GRM). For family data where everyone is related, you could just use the second GRM.  




