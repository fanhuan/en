---
layout: post
title: How Low is Low?
categories: [notes]
tags: [GWAS]
---

You have decided to do whole genomic sequencing (WGS) for your research project. You contacted your sequencing service provider. The first question you will get is: how much data do you need.

What they are actually asking is: what is the sequencing depth, or sometimes referred to as coverage are you expecting.

We all know that coverage limits the kind of analysis we could carry out. But how much coverage is enough? 

In [Hemstrom 2024](https://www.nature.com/articles/s41576-024-00738-6), they tried to define what is Low-coverage WGS. They really tried; they put it into the glossary part:

```
Low-coverage whole-genome sequencing: 
Whole-genome sequencing (WGS)with small numbers of reads covering most genomic loci (low coverage);
the number of reads constituting low coverage varies widely depending on the discipline, 
methodology and research question. Low-coverage WGS often requires genotype likelihood-based methods.
```

OK. So what have we got from these sentences? That "the number constituting low coverage varies widely depending on the discipline, methodology and research question". This means no matter which discipline, which methodology and what kind of research questions you have, you still do not know what is considered low-coverage! But once you've decided that your coverage is indeed low for your perticular circumstance, you should use "genotype likelihood-based methods".

Wow. Where do we start. Maybe let's understand more about this "genotype likelihood-based methods" and it might help us understand when we need to use it and back calculate what is considered low-coverage. (Later I realize this deserves [another post]())

They cited an attack, sorry, no, a [comment](https://onlinelibrary.wiley.com/doi/10.1111/mec.17415) on a pretty famous paper on the inbreeding of [North American wolves](https://onlinelibrary.wiley.com/doi/10.1111/mec.17231). In the wolf paper, the sequencing coverage is 7X. Wow OK that actually sounds low. Imaging if you have a heterozygous site, you won't have five reads to support either, let alone the PCR duplication, which can be actually very high (5% to 50% in my current dataset). OK I would say anything below 10X is a no-brainer low. Later I also discovered this paper used RAD-seq. 7X coverage RAD-seq for 437 individuals (ok the sample size is pretty good). Man we need more funding on conservation.

OK back to the main topic. How low is considered low? The comment paper actually investigated on this matter and showed us some data.

![img](https://onlinelibrary.wiley.com/cms/asset/7eb3243a-bffc-4c15-8b1b-d949b6e5f296/mec17415-fig-0001-m.jpg)

This is the meat of the paper. Let's take a look at some of the relevant subplots.

Figure 1c: This is saying the probability of seeing both alleles in a heterozygous locus will reach amost 1 when the read depth is 10. However this is assuming sequence reads are independent (no PCR duplicates) and that each allele is equally likely to be sequenced. So 10 is the absolute low threshold. You should at least do better than 10.

Figure 1d: F-ROH(run of homozygousity), a finer way of estimating inbreed coefficient (F), see [this paper](https://pubmed.ncbi.nlm.nih.gov/34242449/) on Newzealand hihi (a friendly bird) on more details of ROH, stabalizes after the read depth reaches 5. You may say ok this is no problem since the coverage is 7. No. Then mean coverage is 7, meaning a lot of the loci might have <5 coverage.

Figure 1e: H-obs is the percentage of heterozygous sites observed, and it just kept on rising even after 20X. 

Figure 1f: H-exp is the percentage of heterozygous sites calculated based on Hardy-Weinberg Equilibrium. It stabalizes after 10X. But as the authors pointed out, the pattern is clearer than in Figure 1e, since nobody with an H-exp higher than 0.22 had a read depth lower than 10X. This is to say the H-exp is capped by the read depth. 

Figure 1g: Here missingness means missing calls of genotypes at a site for an individual. You can see that only when the read depth reached 15 when the trend stablizes. 

OK, based on this one study, I will just say that 10X is the bare-minimum, and only >20X can be considered safe for a diploid genome.

Please take note on the '>' before 20X. Let me emphasize. This is not the mean, but the min! If you tell your sequence service provider that you want 20X, you might end up with lots of samples or loci under 20X, even under 10X. I took a brief look on the dataset that I am working on right now. There is indeed a strong correlation between the mean depth of the variants called, and the mean depth of the sequencing effort (r close to 0.9). However the ratio between the two is between 0.5 to 0.75. That is to say in the worse case, only half of the reads were useful in calling the variants. That translate to 27X(0.75) to 40X(0.5) of sequencing effort. This ratio is negatively correlated with the duplication rate (r close to -0.8). Maybe you can go for 30X, and resequence the ones with low variant coverage later.

Good luck to everyone on securing a bigger funding!




