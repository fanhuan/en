---
layout: post
title: Fine Mapping
categories: [notes]
tags: [GWAS]
---

# What is fine mapping

Due to [LD](https://fanhuan.github.io/en/2024/12/02/LD-Prunning/), a lot of SNPs in the same region will be showing the same genotype-phenotype correlation. Fine mapping is the process of narrowing down to the causual variant by distinguishing the hitchhiking ones.

# Why do we need fine mapping?

I can see two senarios. 

1. When we have WGS data, it is usually not necessary to test very variant (precisely due to LD). Testing SNP A will give you almost the same results from testing SNP B if they are tightly linked, or in LD. You can do some LD pruning and test the representatives. However, the representatives were chosen at random and you might have actually removed the causual variant from the testing dataset. 

2. Even if we have tested every single variant, again, you will need a way to distinguish the causual ones versus the hitchikers.

So one thing worth pointing out is that the fine mapping will be carried out on the full dataset.

