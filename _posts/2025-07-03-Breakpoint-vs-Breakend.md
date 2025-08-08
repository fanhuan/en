---
layout: post
title: Breakpoints vs Breakends
categories: [notes]
tags: [bioinformatics]
---

Have been working on structual variations recently and came across some new concepts. 







Before that some brief recap on the __Alternative allele field format__ (section 1.2.5). If the ALT column starts with left angle bracket (<), it suggests an IMPRECISE structual variant. Being imprecise means that the values in the INFO column (END, SVLEN etc.) is estimated to the best of the mapping info.  


Among which I found two confusing ones: breakpoints and breakends.

Breakpoint is a general term. It is the precise positions in the genome where the DNA is broken and rearranged. In a perticular SV, is the start or end coordinate, precisely or imprecisely but to the best estimation. 

Then what are breakends? 

I was first introduced the idea of "breakends" in the manta user guide (btw it still uses python 2.7 and has not been and will not be updated since 2019; I guess everyone is doing long reads now).

```
Manta divides the SV and indel discovery process into two primary steps: (1) scanning the genome to find SV associated regions and (2) analysis, scoring and output of SVs found in such regions.

Build __breakend__ association graph In this step the entire genome is scanned to discover evidence of possible SVs and large indels. This evidence is enumerated into a graph with edges connecting all regions of the genome which have a possible __breakend__ association. 
```
According to the VCF specification (v4.1) there are only 6 types of structual variants (SVTYPE) and they are:

- DEL: deletion
- INS: insertion
- DUP: duplication
- INV: inversion
- CNV: copy number variation
- BND: Breakend

So breakend is one of them. 

The first five are pretty self-explainatory. There is a whole section decicated to breakend in the VCF specification: __5.4 Specifying complex rearrangements with breakends__.

```
An arbitrary rearrangement event can be summarized as a set of novel adjacencies. Each adjacency ties together 2 breakends. The two breakends at either end of a novel adjacency are called mates.
```

Here we first need to understand what is a novel adjacency, which is a new connection between two genomic positions that are not adjacent in the reference genome, suggesting a structural variant in the sample genome.



