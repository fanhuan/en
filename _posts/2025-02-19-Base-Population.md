---
layout: post
title: Base Population and Why It Matters
categories: [notes]
tags: [PG]
---

In the `.fam` file prepared for `plink` , there are two columns for you to specify one's father (PID) and mother (MID) in this dataset, `0 if unknown`. Those with both PID and MID as 0 are considered as __founders__. Note that "By default, if parental IDs are provided for a sample, they are not treated as a founder even if neither parent is in the dataset." In that case you need to manually make them founders via `--make-founders`.  

Why do we need founders? Because only they are included in some calculations such as `minor allele frequencies/counts` or `Hardy-Weinberg equilibrium tests`, both related to the concept of __base population__.

The probability that two alleles are IBD has to be defined with respect to a base (reference) population; that is, the two alleles are descended from the same ancestral allele in the base population.


