---
layout: post
title: UTR Prediction
categories: [notes]
tags: [bioinformatics]
---

I was trying to prioritize some variants manually, after all the GWAS tests and fine mapping, to see whether the mutation it predicts in the protein is in the relevant domain and can cause actual structual changes. However when I wrote a [script](https://github.com/fanhuan/script/blob/master/mutate_aa_genome.py) to generate the aa sequence with this mutation, the aa sequence was the same. What is going on?

This is the full annotation of this variant through [SnpEff](https://pcingola.github.io/SnpEff/) 

```
ANN=G|missense_variant|MODERATE|START_CODON_4_2844041_2844043|g17705|transcript|g17705.t1|protein_coding|11/15|c.721A>G|p.His241Asp|1826/2931|721/2562|241/853||WARNING_TRANSCRIPT_MULTIPLE_STOP_CODONS,G|synonymous_variant|LOW|START_CODON_4_2844041_2844043|g17705|transcript|g17705.t2|protein_coding|11/14|c.1803A>G|p.Val601Val|1803/2682|1803/2682|601/893||,G|intragenic_variant|MODIFIER|GENE_4_2844041_2871639|GENE_4_2844041_2871639|gene_variant|GENE_4_2844041_2871639|||n.2866998A>G||||||,G|non_coding_transcript_variant|MODIFIER|TRANSCRIPT_4_2844041_2871293|null.22587|transcript|TRANSCRIPT_4_2844041_2871293|pseudogene||||||||,G|non_coding_transcript_variant|MODIFIER|TRANSCRIPT_4_2844041_2871639|null.22586|transcript|TRANSCRIPT_4_2844041_2871639|pseudogene||||||||
```

We can see that is is very long. There are multiple transcripts that it is involved, seperated by comma (,). The format for each transcript is:

| # | Field | Meaning | Example
|---|---|---|
| 1 | Allele | the ALT allele being annotated (G) | G
| 2 | Annotation | effect, as a Sequence Ontology term | missense_variant
| 3 | Impact | HIGH / MODERATE / LOW / MODIFIER | MODERATE
| 4 | Gene_Name | gene symbol | START_CODON_4_2844041_2844043
| 5 | Gene_ID | gene identifier | g17705
| 6 | Feature_Type | transcript, gene_variant, etc. | transcript
| 7 | Feature_ID | transcript/feature identifier | g17705.t1
| 8 | BioType | protein_coding, pseudogene, etc. | protein_coding
| 9 | Rank/Total | exon (or intron) rank / total | 11/15
| 10 | HGVS.c | nucleotide change (coding coords) | c.721A>G
| 11 | HGVS.p | amino-acid change | p.His241Asp
| 12 | cDNA_pos/len | position in cDNA / cDNA length | 1826/2931
| 13 | CDS_pos/len | position in CDS / CDS length | 721/2562
| 14 | AA_pos/len | residue position / protein length | 241/853
| 15 | Distance | distance to feature (for intergenic) |
| 16 | Errors/Warnings | annotation QC messages | WARNING_TRANSCRIPT_MULTIPLE_STOP_CODONS

chr4  stringtie2utr  five_prime_UTR  2863596  2863618  +  .  transcript_id "g17705.t1"; ...



