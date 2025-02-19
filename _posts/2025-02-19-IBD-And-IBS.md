---
layout: post
title: IBD and IBS
categories: [notes]
tags: [PG]
---

# IBD

Identity-by-descent, also known as identical-by-descent. In [Speed and Balding 2015](https://doi.org/10.1038/nrg3821), it is defined as

```
The phenomenon whereby two individuals share a genomic region as a result of inheritance from a recent common ancester, where 'recent' can mean from an ancestor in a given pedigree, or with on intervening mutations event or with no intervening recombination event.
```

This concept is tightly linked to 

```
Traditional measures of relatedness, which are based on probabilities of IBD from common ancestors within a pedigree, depend on the choice of pedigree.
```

Therefore the [A matrix](fanhuan.github.io/en/2024/10/14/A-Matrix/) is based on IBD. 

In another review paper, [Powell 2010](https://www.nature.com/articles/nrg2865) defined it as "alleles that are descended from a common ancestor in a base population". You can see the two definitions are slightly different. The former uses "genomic region" as the unit whereas the latter uses "alleles". Alleles are versions of genes, where as "genomic region" can be non-genic, also can be of any length, so the former is more generic.

# IBS

Identity-by-state, also known as identical-by-state. This concept is relatively simple. It just means that two things, be it alleles or genomic regions, they are the same in two different individuals, iregardless whether it is IBD. This sounds familiar right? The relationship between IBD and IBS is like the one between orthologs and homologs. 

IBS is waht we see in the current dataset, and is usually used to calculate the [G matrix](http://fanhuan.github.io/en/2024/12/12/GRM/) with unknown pedigree.


