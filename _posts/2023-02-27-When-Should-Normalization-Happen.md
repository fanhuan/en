---
layout: post
title: How Should Normalizatin Happen
categories: [data analysis]
tags: [stats]
---

I spent a lot of time thinking about the normalizatin problem in microbiome dataset. 

The commom practice right now is rarefraction to the lowest number of reads. But apparently that is not the best way of doing things (McMurdie 2014 PLoS Computational Biology). I tried to look for ways that work best for my dataset and after studying Lorens-Rico 2021 Nature Communication very carefully, I settled on TMM(trimmed mean of M values). However there is still one question, there are so many steps I need to take in my data preparation pipeline such as removing low abundant ASVs, singletons, or subseting for a perticular reason (a specific tissue only, or fungi from a perticular trophic mode). When should TMM happen?

Since TMM is fixing the problem of uneven library size, obviously it has to happen sooner than later. But the ASVs calling part is not set in stone. Should removing low abundant ASVs happen first? To answer this questin,
I decided to read Nearing 2022 Nature Communication (wow it seems like everyone has a NC paper!) on _Microbiome differential abundance methods produce different results across 38 datasets_.

This paper talks about different ways of identifying differentially abundant microbes. I've actually asked chatGPT about the same question and it gave me the answer as follows:

1. Differential abundance analysis: This approach involves comparing the abundance of each species in the treatment group to the control group to identify which species are differentially abundant in the treatment group. This can be done using a variety of statistical methods, such as t-tests, ANOVA, or generalized linear models. The advantage of this approach is that it can identify individual species that are responding to the treatment, but it may not capture interactions between species.

2. Co-occurrence network analysis: This approach involves constructing a network of co-occurring species and identifying modules or clusters of species that are correlated with the treatment group. This approach can be useful for identifying groups of species that are responding to the treatment, but it may not capture individual species that are responding.

3. Machine learning: This approach involves using machine learning algorithms to predict the response of each species to the treatment based on its abundance and other metadata. This can be a powerful approach if there are complex interactions between species or if the response of individual species is difficult to predict based on abundance alone.

4. Correlation analysis: This approach involves identifying species that are positively or negatively correlated with the treatment group. This can be done using correlation coefficients such as Pearson's correlation coefficient or Spearman's rank correlation coefficient. The advantage of this approach is that it can identify species that are responding to the treatment, but it may not capture the direction or magnitude of the response.

But as you already know my focus is on the pre-processing of the data, and Table 1 tells you all. Basically for differnet statistical tests, different normalizatin methods are used (or no normalization at all).

Therefore for my current analysis, PERMANOVA and Mantel's test, I need to see what would be the best normalization method. Therefore it is important to keep original phyloseq objects without further pre-processing.

So back to my own question: what kind of normalization do I need for 
1. PERMANOVA test
2. Mantel's test.


