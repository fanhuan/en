---
layout: post
title: SNP Filtering - Basic Principles
categories: [notes]
tags: [GWAS]
---



With high sequencing coverage, big genomes and big samples size, you can easily get dozens of millions of SNPs. Of course you are not going to work with all of them. Therefore filtering becomes very important. In this article we will talk about some basic principles for SNP filter, how to do it using `plink` which is fast and versatile, and discuss some examples or tutorials on how people are doing it in the field right now.

# Part 1: Hard filtering

## Removing low quality ones:

1. Remove SNPs with high missing call rates. 

   ​	Missing calls are shown as  `./.` There are also half missing calls which looks like `1/.` or `./0`. However they would have been taking care of when converting your vcf to bed using `plink` by using the option `--vcf-half-call m`. This is treating half calls as **M**issing calls. Other options includes: '**haploid**'/'**h**': Treat half-calls as haploid/homozygous (the PLINK 1 file format does not distinguish between the two). This maximizes similarity between the VCF and BCF2 parsers. '**reference**'/'**r**': Treat the missing part as reference.

   ​	After all half missing calls are taken care of, can use `--geno` to filter out all variants with missing call rates exceeding the provided value (default 0.1, this means if you just specify --geno without any number it will use 0.1). The lower the more SNPs will be filtered out.

2.  





Tools.

1. Non-ML based
2. ML based
   1. DeepVariant is a caller so it does not do the filtering per say. However, being a highly accurate caller, it already sets the bar for artefacts to go into the results high. Btw it is usually used together with GLnexus, which is a scalable merging tool that can deal with hundres and thousands of samples with big genomes, with heavy disk space usage as compensation.
