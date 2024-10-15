---
layout: post
title: Genomic Estimated Breeding Values
categories: [notes]
tags: [GWAS]
---

What are we talking about when we talk about the genetic architecture of a complex trait.
1. We would like to know the overall contribution of the genetic variants to the trait variation
2. What are the genetic polymorphisms(variants) that cause trait variation among individuals.
3. Effect size of those genetic variants on the trait
4. What are the genes those variants are on and what is the underlying genetic regulatory mechanisms.

Genomic Estimated Breeding Value(GEBV) is related to the first point. It is an very important measurement in the molecular breeding world. Basically it is the best prediction you can have for the penotypic performance of an individual based on its genenic makeup (and other fixed effects if any in the model, such as sex). Firstly three level of designs from small to big:

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


There are two ways to construct the random effect part for predicting the **Genomic Estimated Breeding Value (GEBV)**, sometimes known as the left mixed model using a **Genomic Relationship Matrix (GRM)** (n * n)and the right mixed model using a **SNP matrix** as the random effects. Although the predictions will often be similar, they won't be exactly the same.

### 1. **Using the GRM in GEBV Prediction**:
   - **GRM** summarizes the genetic relationships between individuals by incorporating the genome-wide additive effects of all SNPs.
   - When you use the GRM as a random effect in a model like **G-BLUP**, you are not directly modeling the effect of each SNP. Instead, you’re leveraging the genetic relatedness (as summarized by the GRM) to predict the aggregate genetic effect on breeding values.
   - **GRM-based GEBV**: The GEBV is predicted based on the relationship between individuals as captured by the GRM, which accounts for the genome-wide genetic similarity.

### 2. **Using the SNP Matrix in GEBV Prediction**:
   - **SNP matrix** includes the effects of individual SNPs, with each SNP coded as 0, 1, or 2 (for homozygous reference, heterozygous, and homozygous alternative alleles, respectively).
   - When the **SNP matrix** is used directly as the random effect (e.g., in Bayesian Ridge Regression or other marker-based models), each SNP effect is estimated individually. These SNP effects are then summed to predict the GEBV.
   - **SNP-based GEBV**: The GEBV is predicted as the sum of the individual SNP effects, allowing you to capture the contribution of each marker separately.

### Key Differences:
- **GRM**:
   - The GRM aggregates genome-wide information and models the overall genetic relationship between individuals. It doesn't provide explicit estimates for individual SNP effects.
   - It is a **more efficient approach** when the goal is to predict GEBVs based on additive genetic variance without needing to estimate each SNP effect.

- **SNP Matrix**:
   - The SNP matrix models the contribution of each SNP separately, providing more granular estimates of the marker effects.
   - It can be computationally **more intensive** to estimate effects for thousands or millions of SNPs individually, but it provides more information about the specific contributions of each SNP.

### Will GEBVs Be the Same?
- **Similar but not identical**: In many cases, the GEBVs predicted from the GRM and SNP matrix will be quite similar, especially if the population structure is simple and there are no strong dominance or epistatic effects. This is because the GRM is a summary of the same SNP data that would be used in the SNP matrix.
  
- **Small differences**: If you use the SNP matrix directly, you may capture more specific effects of individual SNPs, which could lead to **slightly different GEBV estimates**. Additionally, if there are important SNPs with large effects (e.g., major genes affecting the trait), the SNP matrix may capture these more explicitly than the GRM.

### Practical Example:
- **G-BLUP (GRM)**: In G-BLUP, the GRM accounts for the total additive genetic variation, so you get GEBVs based on the overall genetic similarity between individuals.
- **Bayesian models (SNP matrix)**: In models like Bayesian Ridge Regression, the SNP effects are modeled directly, and GEBVs are computed as the sum of these effects.

### Conclusion:
The **GEBV** predicted using the **GRM** and using the **SNP matrix** will generally be similar but **not necessarily identical**. The GRM provides a more aggregate, efficient method for modeling the additive genetic effect, while the SNP matrix gives more detailed insights into individual SNP contributions. The choice between the two depends on whether you want to capture the total genetic relationship (GRM) or the individual SNP effects (SNP matrix).
