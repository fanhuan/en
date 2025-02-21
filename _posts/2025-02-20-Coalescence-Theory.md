---
layout: post
title: Coalescence Theory
categories: [notes]
tags: [PG]
---

When I was doing some reading on IBD, I ran into this passage in [Powell 2010](https://www.nature.com/articles/nrg2865).

__"Traditionally, the probability that two alleles are IBD was most often calculated from a known pedigree and so the individuals at the top of the pedigree (the founders) form a natural base population. However, it is becoming common to use data on genetic markers such as SNPs to estimate the probability of being IBD without reference to a known pedigree and, in this case, there is no obvious base population. Moreover, the concept of IBD seems to conflict with the well-established coalescence theory in which all alleles are descended from a common ancestor but at different times in the past."__

Then I was a bit lost. Why are they in conflict? I asked ChatGPT and this is the answer:

The seeming conflict comes from how “identity by descent” (IBD) and coalescence theory define “common ancestry” over different time scales and with different reference points.

### IBD in Pedigree Context

- **IBD Definition:**  
  In a pedigree, IBD typically means that two alleles are identical because they were inherited from the same recent common ancestor.  
- **Base Population:**  
  When you work with a known pedigree, you often define a set of founders as your “base population.” Alleles are considered IBD if they are identical by descent from those founders.

### Coalescence Theory

- **Coalescence Concept:**  
  Coalescence theory tells us that if you trace the ancestry of any pair of alleles far enough back in time, they will eventually converge on a single common ancestral allele.  
- **Universal Common Ancestor:**  
  This means that, theoretically, all alleles in a population are IBD if you go far enough back—because they all share an ancestor (for example, the most recent common ancestor of the species).

### The Conflict

- **Time Scale and Practical Use:**  
  - **IBD:** In practice, IBD is used to assess recent shared ancestry. It’s meaningful when you have a defined base population or cutoff time, so you can say, “These two alleles come from the same recent ancestor.”
  - **Coalescence Theory:** Coalescence does not have a built-in cutoff; it implies that, given infinite time, all alleles are IBD. This makes the concept trivial if you ignore time scales.
  
- **No Natural Base Population:**  
  When using genetic markers like SNPs without a known pedigree, there's no obvious set of founders. In coalescence theory, every allele is descended from a common ancestor if you go back far enough. This contrasts with the practical use of IBD, where we’re interested in whether two alleles share a recent common ancestor, not just any ancestor in the distant past.

### In Summary

- **IBD (Traditional/Practical Use):**  
  It’s about recent common ancestry relative to a defined base population. Two alleles are considered IBD if they are inherited from the same recent ancestor.
  
- **Coalescence Theory:**  
  It shows that all alleles coalesce into a single ancestral allele over time, meaning that if you extend the time horizon indefinitely, every pair of alleles is IBD.  

Thus, the conflict arises because coalescence theory makes the notion of IBD universal (all alleles are IBD eventually), while in practice, we want to quantify recent relatedness using IBD. This is why when using genetic markers without a known pedigree, researchers must define an arbitrary or biologically meaningful base population or time threshold for IBD to be useful.

Then the article went on about how this conflict is dealt with: 

"In practice, this conflict has been ignored by using IBD concepts for recent common ancestors and coalescence analysis for distant common ancestors; however, the two categories of ancestor merge, especially when using dense SNP or DNA sequence data."

Sounds familiar? This is exactly the problem we discussed in the [quantitative genetics vs. population genetics post](https://fanhuan.github.io/en/2024/09/25/PG-VS-QG/). In quantitative genetics, we usually working with populations with recent common ancestors, sometimes even with clear pedigree; while in population genetics, we usually have little to no understanding of the pedigree and relies solely on molecular markers. While just as this sentence described, now that we have whole genome information on various types of populations combined, how things are done differently before needs to be unified. 

So, is coalescence theory still relevant?

