---
layout: post
title: GWAS and Its Peaks
categories: [notes]
tags: [bioinformatics]
---

When we do a manhattan plot for GWAS results, we are expecting to see sharp peaks, the sharper the better. But how about those isolated points with very low p-values, even after adjustment/punishment? Why are they less trustworthy? It is something that I know for a fact, but always having problem explaining to people who do not do GWAS. Today I'd like to solve this problem once and for all (wow ambitious)!

At the heart of the problem is something called Linkage Disequilibrium (LD). This word has been the center of my universe in the recent couple of years. Everything dated back in 2010 in Okinawa; LD and coalescent is the center of every theory and every lecture, together with all these selections. 

**Linkage Disequilibrium and Signal Coherence**

When you see a sharp peak with multiple SNPs showing strong associations, it typically reflects the underlying linkage disequilibrium (LD) structure of the genome. SNPs in close proximity tend to be inherited together, so a true causal variant should create a signal that extends across nearby correlated SNPs. An isolated significant SNP surrounded by non-significant variants suggests the signal might not be reflecting a genuine biological effect in that genomic region.

**Technical Artifacts and Genotyping Errors**

Isolated significant SNPs are more likely to represent technical problems like genotyping errors, batch effects, or platform-specific artifacts. These issues typically affect individual SNPs rather than entire LD blocks. Quality control procedures can miss some of these problems, especially if they're systematic across cases and controls.

**Population Stratification Issues**

Inadequately corrected population structure can create spurious associations at individual SNPs, particularly those with unusual allele frequency patterns across ancestral groups. Well-designed studies use principal components or other methods to control for this, but isolated signals might indicate residual stratification.

**Multiple Testing Considerations**

While you mention adjusted p-values, the genomic context matters for interpretation. A single SNP reaching genome-wide significance (typically 5×10⁻⁸) in isolation is statistically significant but lacks the biological plausibility that comes with seeing the expected LD pattern around a true association.

**Biological Plausibility**

Clustered signals often coincide with known genes, regulatory elements, or functional annotations, providing biological context. Isolated SNPs in gene deserts or without obvious functional relevance require more scrutiny.

However, isolated SNPs aren't automatically false positives - they could represent rare variants with large effects, structural variants not well-captured by standard arrays, or associations in regions of low LD. The key is to evaluate them with additional evidence like replication studies, functional annotation, and deeper sequencing.



