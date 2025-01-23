---
layout: post
title: GRM for Family Data
categories: [notes]
tags: [GWAS]
---

## Flavor 0: Unrelated individuals

In a [previous post](https://fanhuan.github.io/en/2024/12/12/GRM/) we talked about Proferssor Yang Jian's landmark [2010 NG paper](https://www.nature.com/articles/ng.608). It is for unrelated individuals, where it is assumed that the average relationship between all pairs of individuals in 0 and the average relationships of an individual with him- or herself is 1 (see the last paragraph of the __Statistical framework__ of the __ONLINE METHODS__ section). There is a slightly differen flavor for this version 

```--make-grm-alg 0
The default value is 0, and the GRM is calculated using the equation sum{[(xij - 2pi)*(xik - 2pi)] / [2pi(1-pi)]} as described in Yang et al. 2010 Nat Genet. If the value = 1, the GRM will be calculated using the equation sum[(xij - 2pi)(xik - 2pi)] / sum[2pi(1-pi)]*.
```

For my data it does not make a big difference.


## Flavor 1: Inbred data

On the [same page](https://yanglab.westlake.edu.cn/software/gcta/#MakingaGRM) as the usual `--make-grm`, there is an option called `--make-grm-inbred Make a GRM for an inbred population such as inbred mice or inbred crops.` Note the difference between inbred data and family data. Inbred data is usually referring to low, very low degree of heterozygosity, which is usually a result of generations of inbred. Whereas family data still have a good amount of heterozygosity. This definition focus on the pedigree. In the Citation part, two papers were mentioned, Yang 2010 NG and Yang 2011 AJHG (the GCTA paper). However when I searched for the word `inbred` in both papers, no hits. Therefore theoretically, I do not know what happens when you use this option. However, I did compapred the two GRMs resulted from the two options with the same input data, let's call them `GRM` and `GRM_inbred`, and at least for my data, `GRM` = `GRM_inbred` * 2. 


## Flavor 2: Family data.

GCTA offers an [implementation](https://yanglab.westlake.edu.cn/software/gcta/#GREMLinfamilydata) of this method proposed by [Zaitlen et al. 2013 PLoS Genetics](https://doi.org/10.1371/journal.pgen.1003520). Their documentation is self-sufficient. Here I am just trying to summarize in my own words. 








