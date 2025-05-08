---
layout: post
title: Genotype Likelihood
categories: [notes]
tags: [Stats]
---

Yesterday when we were talking about [sequencing depth](https://fanhuan.github.io/en/2025/04/16/Sequencing-Coverage/) we ran into a word: genotype likelihood. I know what is genotype; I also know what is likelihood; but what is genotype likelihood?


Before we start, let's do a quick recap on the difference between probability and likelihood, since they are usually a mixtures in my head unless I really try to focus on their differences.


- Probability = What is the chance of this outcome, given a model or known parameters? P(D∣θ)

- Likelihood = How plausible is this model (or parameter value), given the data I observed? L(θ∣D)

Therefore probabilies are used in simulation from known models, and likelihood is used in inferring model parameters from observed data (what we are doing most of the time).

OK, so from our understanding of genotype and likelihood, genotype likelihood should be L(AA/Aa/aa|mapping results).

Or is it?


so, some sort of quality score, like the QUAL column in a vcf?
P(data∣no variant).
Phred Quality Score (Q)=−10×log10​(P)

So:

    QUAL = 30 → 1 in 1000 chance the site is not a real variant

    QUAL = 50 → 1 in 100,000 chance it's a false positive

 Reference chain:
 Kardos 2024 Molecular Ecology -> 2019 Bertrand MEE -> 2016 Vieira Bioinformatics -> 2009 The sequence alignment/map format and SAMtools


