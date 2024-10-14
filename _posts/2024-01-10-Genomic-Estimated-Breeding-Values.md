---
layout: post
title: Genomic Estimated Breeding Values
categories: [notes]
tags: [GWAS]
---

OK! So Genomic Estimated Breeding Value(GEBV) is an very important measurement in the molecular breeding world. Basically it is the best prediction you can have for the penotypic performance of an individual based on its genenic makeup (and other fixed effects if any in the model, such as sex). Firstly three level of designs from small to big:

1. Pedigree: clear relationships within Families represented by a family-tree.  

2. Family: a group of individuals with a degree of genetic relatedness, such as parents and their offspring, or siblings, but might not have a clear family-tree, only some pedigree structure.

3. Population: a much bigger concept than family and no pedigree structure (the relatedness can be inferred from DNA markers though).

Things that might influence phenotypes (explainatory variables):

1. environment 
2. fixed effect such as gender, country, year of birth)
2. additive gene action: the total effect of a particular trait is the sum of the individual effects of __alleles__ at different loci. There are no interactions between alleles at different loci (no __epistasis__) or between multiple alleles at the same locus (no dominance).
3. dominance: interactions between multiple alleles at the same locus (Aa and AA has the same trait, which is not additive).
4. epistasis: interaction between different loci
5. G by E: interaction between genetics and environment.

Measurement of similarity/resemblance between quantitative traits: covariance(yi, yj)/variance(y)

Typical models:

Phenotype = genetic effect + fixed effect (country, year of birth) + random effect (family environment, individual environment)

And remember, LMM is all about how we partition the residuals from population mean. The less we leave it up to the sigma(residual standard error), the more the model explains the variance in y.

V(phenotype) = V(genetic effects) + V(family environment) + V(individual environment) + sigma

Then I forgot why we also need to consider the covariance matrix.
Cov(phenotype_i, phenotype_j) = Cov(genetic effects) + Cov(family environments) (where did the individual environment go? I guess there is no covfor that).

Cov(family environments) = 0 if different families, VCE if same family. What is VCE again?

What are we talking about when we talk about the genetic architecture of a complex trait.
1. We would like to know the overall contribution of the genetic variants to the trait variation
2. What are the genetic polymorphisms(variants) that cause trait variation among individuals.
3. Effect size of those genetic variants on the trait
4. What are the genes those variants are on and what is the underlying genetic regulatory mechanisms.

Correlation(cor), covariance (cov) and variance (var) and covariation and varation. 

correlation is standardized covariance. folumar of correlation is cor(y1, y2) = cov(y1, y2)/sqrt(var(y1)var(y2)). If the variables (y1, and y2) are already normalized (mean = 0, sd=1), then cor(y1, y2) = cov(y1, y2)

即生瑜，何生亮






