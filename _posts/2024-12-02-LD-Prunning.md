---
layout: post
title: LD Prunning
categories: [notes]
tags: [stats]
---


In the era of whole genome sequencing of thousands of individuals, we are facing the problem of not too few genetic variants, but too many. A major task is to filter those variants. Recently there is a very good review paper on this topic by [Hemstrom et al](https://www.nature.com/articles/s41576-024-00738-6). One of the way of filtering is based on **linkage disequilibrium (LD)**.

### **1. What is Linkage Disequilibrium (LD)?**
- **LD** measures the statistical association between alleles at different loci.
- If two loci are in LD, the allele combinations at these loci occur more or less frequently than expected based on their individual allele frequencies.
- LD can be quantified using metrics like \( D' \), \( r^2 \), or \( D \):
  - \( r^2 \): Measures the correlation between alleles at two loci, ranging from 0 (no LD) to 1 (complete LD).
  - \( D \): Measures the deviation of observed haplotype frequencies from expected under linkage equilibrium.

### **2. Real-World Implications**
1. **Mapping Genes**:
   - LD is used in genome-wide association studies (GWAS) to link genetic markers to traits.
   - High LD regions may indicate physical proximity between a marker and a causal variant.

2. **Population Genomics**:
   - LD patterns provide insights into recombination landscapes, population structure, and demographic history.

3. **Selective Sweeps**:
   - Strong positive selection can maintain high LD around a beneficial allele, even in regions with moderate recombination rates.

Why do we want/need to ca

The relationship between **recombination rate** and **linkage disequilibrium (LD)** is a key concept in population genetics. LD describes the non-random association of alleles at two or more loci, while recombination rate determines how frequently genetic material is exchanged between loci during meiosis. Here's how they are related:


I'd like to do some PCA on a genome sequencing dataset. I have a filtered vcf and all is good, but there is one more thing that I need to do before a PCA.

LD prunning.

Previously we explained what LD is and 

---




---


---



---



