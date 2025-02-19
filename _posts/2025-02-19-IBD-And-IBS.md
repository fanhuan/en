---
layout: post
title: IBD and IBS
categories: [notes]
tags: [PG]
---

# IBD

Identity-by-descent, also known as identical-by-descent. In [Speed and Balding 2015](https://doi.org/10.1038/nrg3821), it is defined as the __"phenomenon whereby two individuals share a genomic region as a result of inheritance from a recent common ancester, where 'recent' can mean from an ancestor in a given pedigree, or with on intervening mutations event or with no intervening recombination event."__

In another review paper, [Powell 2010](https://www.nature.com/articles/nrg2865) defined it as __"alleles that are descended from a common ancestor in a base population"__. You can see the two definitions are slightly different. The former uses __"genomic region"__ as the unit whereas the latter uses __"alleles"__. Alleles are versions of genes, where as "genomic region" can be non-genic, also can be of any length, so the former is more generic. Also, the latter emphasized on the concept of __"base population"__. The probability of IBD is sometimes referred to as __F__, and it __"has to be defined with respect to a base (reference) population; that is, the two alleles are descended from the same ancestral allele in the base population.__" Why so? As you can imaging, if an allele is very rare in the base population, then the possibility of IBD is very high. On the contrary, if an allele is very prominant in the base population, two individuals having the same allele could be due to chance. See another [post]() on how to determine the base population. 

"If the two alleles are in the same diploid individual then F is the __inbreeding coefficient__ of the individual at this locus." See more on how IC is calculated in [this post](http://fanhuan.github.io/en/2025/02/17/Inbreeding-Coefficient/).

The probability of IBD ,or __F__, is tightly linked to __"Traditional measures of relatedness, which are based on probabilities of IBD from common ancestors within a pedigree, depend on the choice of pedigree".__ If the pedigree is known, the expected IBD is [A matrix](fanhuan.github.io/en/2024/10/14/A-Matrix/). When the pedigree is unknown, IBD relationships are estimated from IBS. 

# IBS

Identity-by-state, also known as identical-by-state. This concept is relatively simple. It just means that two things, be it alleles or genomic regions, they are the same in two different individuals, iregardless whether it is IBD. This sounds familiar right? The relationship between IBD and IBS is like the one between orthologs and homologs. 

IBS is waht we see in the current dataset, and is usually used to calculate the [G matrix](http://fanhuan.github.io/en/2024/12/12/GRM/) with unknown pedigree. As you can see this can lead to erroneous inference because a consistent base population is not used. 

There we borrow an illustration from [Powell 2010]((https://www.nature.com/articles/nrg2865) to demonstrate the difference between IDB and IBS.

![img](https://fanhuan.github.io/en/images/Powell2010.png)

So in this  figure, as long as the letter is the same, they are IBS, so all the Gs and all the Ts are IBS respectively. However, you also need to have the same background color to be IBD. For example, C1 and C2 are IBD, B3 and B4 are not IBD, C4 and C5 are not IBD either. Note that this relationship is usually considered within the same generation, not crossing generations. Another thing to note in this figure is that the Base population used for the estimation of IBD coefficients should be B1, B2, B3 and B4, not the current C1 to C5. This is why you need to specify the founders or any know pedigree info in the .fam file. I wonder whether `gcta` takes this info? I tried but it does not :(

