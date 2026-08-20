---
layout: post
title: The MultiSuSiE Paper
categories: [1paperAday]
tags: [Stats]
---

Today's paper is [Rossen 2026 Nature Genetics](https://www.nature.com/articles/s41588-025-02450-5), _MultiSuSiE improves multi-ancestry fine-mapping in All of Us whole-genome sequencing data_. I am reading is because I use SuSiE for [fine mapping](https://fanhuan.github.io/en/2026/06/03/Fine-Mapping/).

1. The data is all public, from [All of Us](https://www.nature.com/articles/s41586-023-06957-x). Impressive dataset. For a genome size of 3 billion bp, "all of Us identified more than 1 billion genetic variants, including more than 275 million previously unreported genetic variants, more than 3.9 million of which had coding consequences". They also have longitudinal electronic health record which allowed them to evaluate 3,724 genetic variants associated with 117 diseases and found high replication rates across both participants of European ancestry and participants of African ancestry. You can start a genotyping company based on those 3724 variants if you work with people from those ancestries.
2. In simulation, the authors used a balanced design (36K * 3) that matches the size of the European cohort (109K). This is capped by the 36K Latino-ancestry dataset. 
3. The concept of calibration. "To assess calibration, we compared the empirical FDR to (1 − PIP threshold), a conservative FDR upper bound (as in ref. 12), as well as (1 − mean PIP), the expected FDR (which has been reported to be slightly miscalibrated in previous fine-mapping simulations." 

Let's break down this sentence. Calibration is to see whether the predicted FDR match the empirical FDR (FP/(FP+TP) measured in simulation where truth is known. Two ways of calibration are mentioned.

The first one is empirical FDR vs. (1-PIP threshold). Ref 12 is [Weissbord 2020 Nature Genetics](https://www.nature.com/articles/s41588-020-00735-5). In it, FDR is defined as "the proportion of false positives among SNPs with posterior causal probability (posterior inclusion probability (PIP)) above a given threshold (for example, PIP > 0.95), aggregating the results across all simulations". This is to say, only SNPs with PIP > 0.95 are considered in the calculation of FDR, so in theory the FDR calculated this way should be much lower than the FDR calculated based on all the SNPs tested.

The second one is empirical FDR vs (1 - mean PIP). Here 1 - mean(PIP) is known as the expected FDR. To understand why, we need to firstly understand PIP, or posterior inclusion probability.

What is posterior inclusion probability? In the SuSiE paper, [Wang 2020 Journal of the Royal Statistical Society Series B: Statistical Methodology](https://doi.org/10.1111/rssb.12388), it is defined as (equation 2.3)

$$\text{PIP}_j := \Pr(b_j \neq 0 \mid X, y)$$

that is, the given the data (genotypes $X$ and phenotypes $y$.), what is probability that variable $j$ has a non-zero effect ($b_j \neq 0$). 

Now, let's try to understand why 1-mean(PIP) is the expected FDR.

As threshold of PIP(0.95) is almost always higher than mean(PIP), the first one is less tolerant of higher FDR (therefore a more conservative/lower upper bound).

Interestingly, Ref 12 is received on 28 October 2019 and Accepted on 02 October 2020. It cited [SuSiE](https://doi.org/10.1111/rssb.12388), which is also published in 2020, but it was submitted on 01 December 2018, and only accepted on 01 May 2020, from [a different lab](https://stephenslab.uchicago.edu/).  

