---
layout: post
title: GTF Format and UTR Prediction
categories: [notes]
tags: [bioinformatics]
---

I was trying to prioritize some variants manually, after all the GWAS tests and fine mapping, to see whether the mutation it predicts in the protein is in the relevant domain and can cause actual structual changes. However when I wrote a [script](https://github.com/fanhuan/script/blob/master/mutate_aa_genome.py) to generate the aa sequence with this mutation, the aa sequence was the same. What is going on?

This is the full annotation of this variant through [SnpEff](https://pcingola.github.io/SnpEff/) with some editing so it is not a real variant anymore. But the problem is true.

```
ANN=G|missense_variant|MODERATE|START_CODON_1_1000_1002|g12345|transcript|g12345.t1|protein_coding|11/15|c.721A>G|p.His241Asp|1826/2931|721/2562|241/853||WARNING_TRANSCRIPT_MULTIPLE_STOP_CODONS,G|synonymous_variant|LOW|START_CODON_1_1000_1002|g12345|transcript|g12345.t2|protein_coding|11/14|c.1803A>G|p.Val601Val|1803/2682|1803/2682|601/893||,G|intragenic_variant|MODIFIER|GENE_1_1000_28598|GENE_1_1000_28598|gene_variant|GENE_1_1000_28598|||n.23957A>G||||||,G|non_coding_transcript_variant|MODIFIER|TRANSCRIPT_1_1000_28252|null.22587|transcript|TRANSCRIPT_1_1000_28252|pseudogene||||||||,G|non_coding_transcript_variant|MODIFIER|TRANSCRIPT_1_1000_28598|null.22586|transcript|TRANSCRIPT_1_1000_28598|pseudogene||||||||
```

We can see that it is very long. There are multiple transcripts that it is involved in, separated by comma (,). Let's put them into a table:

| # | Field | Meaning | 1 | 2 | 3 | 4 | 5 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 1 | Allele | the ALT allele being annotated | G | G | G | G | G |
| 2 | Annotation | effect, as a Sequence Ontology term | missense_variant | synonymous_variant | intragenic_variant | non_coding_transcript_variant | non_coding_transcript_variant |
| 3 | Impact | HIGH / MODERATE / LOW / MODIFIER | MODERATE | LOW | MODIFIER | MODIFIER | MODIFIER |
| 4 | Gene_Name | gene symbol | START_CODON_1_1000_1002 | START_CODON_1_1000_1002 | GENE_1_1000_28598 | TRANSCRIPT_1_1000_28252 | TRANSCRIPT_1_1000_28598 |
| 5 | Gene_ID | gene identifier | g12345 | g12345 | GENE_1_1000_28598 | null.22587 | null.22586 |
| 6 | Feature_Type | transcript, gene_variant, etc. | transcript | transcript | gene_variant | transcript | transcript |
| 7 | Feature_ID | transcript/feature identifier | g12345.t1 | g12345.t2 | GENE_1_1000_28598 | TRANSCRIPT_1_1000_28252 | TRANSCRIPT_1_1000_28598 |
| 8 | BioType | protein_coding, pseudogene, etc. | protein_coding | protein_coding | | pseudogene | pseudogene |
| 9 | Rank/Total | exon (or intron) rank / total | 11/15 | 11/14 | | | |
| 10 | HGVS.c | nucleotide change (coding coords) | c.721A>G | c.1803A>G | n.23957A>G | | |
| 11 | HGVS.p | amino-acid change | p.His241Asp | p.Val601Val | | | |
| 12 | cDNA_pos/len | position in cDNA / cDNA length | 1826/2931 | 1803/2682 | | | |
| 13 | CDS_pos/len | position in CDS / CDS length | 721/2562 | 1803/2682 | | | |
| 14 | AA_pos/len | residue position / protein length | 241/853 | 601/893 | | | |
| 15 | Distance | distance to feature (for intergenic) | | | | | |
| 16 | Errors/Warnings | annotation QC messages | WARNING_TRANSCRIPT_MULTIPLE_STOP_CODONS | | | | |

So one variant produced five annotations. Two questions jump out: 
1. why are there five entries for what is really one gene with only two transcripts 
2. a missense is nice but why is `g12345.t1` flagged with `WARNING_TRANSCRIPT_MULTIPLE_STOP_CODONS`?

## Where entries 3, 4, 5 come from

The strings `pseudogene`, `null.`, `TRANSCRIPT_1_…` and `GENE_1_…` do not appear anywhere in the GTF. SnpEff fabricated entries 3–5 at database-build time because of how the [BRAKER](https://github.com/Gaius-Augustus/BRAKER) GTF writes its parent lines. Compare the `gene`/`transcript` lines with their child features:

```
gene        … 1000 28598 … +  .  g12345                                       ← bare ID
transcript  … 1000 28598 … +  .  g12345.t1                                    ← bare ID
CDS         … 1000 1449 … +  0  transcript_id "g12345.t1"; gene_id "g12345"; ← proper attributes
exon        … 1000 1449 … +  .  transcript_id "g12345.t1"; gene_id "g12345";
```

SnpEff's GTF parser expects `key "value";` attribute pairs. The `gene` and `transcript` lines in the raw BRAKER gtf output instead put a bare ID in column 9, so the parser extracts no ID and falls back to synthetic, coordinate-based markers. As a result SnpEff parses the locus twice:

- the properly-attributed `CDS`/`exon`/`intron` lines correctly rebuild `g12345` with transcripts `.t1` and `.t2` (entries **1** and **2**) because there are proper "transcript_id" and "gene_id" as the key.
- each unparseable `gene` line becomes a childless phantom gene `GENE_chr_start_end` → `intragenic_variant` (entry **3**), and each unparseable `transcript` line becomes a phantom transcript `TRANSCRIPT_chr_start_end` wrapped in an invented `null.N` gene. With no CDS attached, these default to `pseudogene` / `non_coding_transcript_variant` (entries **4** and **5**).

This is genome-wide: every `gene` and `transcript` line in the file uses the bare-ID format, so the database ends up with a lot of `GENE_*` and `null.*` phantom records. The fix is to normalise the attribute column (give the parent lines real `gene_id "…"; transcript_id "…";` fields) and rebuild. Entries 3–5 then disappear, leaving only the two real transcripts. Here _normalise_ means converting records to a canonical/standard form in data/file engineering. 

## The real puzzle: the `g12345.t1` warning

With the phantoms gone we are left with entries 1 and 2 — the same variant on two transcripts of the same gene: **missense** (`p.His241Asp`) on `.t1` and **synonymous** (`p.Val601Val`) on `.t2`. I mentioned in the beginning that when I tried to apply this mutation to the protein sequence, the sequence did not change. Why did the missense go missing? Is it related to the `WARNING_TRANSCRIPT_MULTIPLE_STOP_CODONS` that `.t1` carries?

Let's take a look at the gtf of this gene. The difference between the two transcripts is that `.t1` carries UTR features added by `stringtie2utr` and `.t2` does not. Sorting `.t1`'s features by position shows the problem:

```
# transcript g12345.t1  
AUGUSTUS       start_codon     1000  1002
AUGUSTUS       CDS             1000  1449
   …
AUGUSTUS       CDS             20367  20499   (phase 2)
stringtie2utr  five_prime_UTR  20555  20577   ← a 5' UTR ~19 kb INTO the coding region
AUGUSTUS       CDS             20578  20731   (phase 1)
   …
AUGUSTUS       stop_codon      28250  28252
stringtie2utr  three_prime_UTR 28253  28598
# transcript g12345.t2 has the same CDS but no UTR lines
```

A 5′ UTR is by definition *upstream* of the start codon. This one sits in the middle of the CDS, downstream of the start codon and inside an intron. When SnpEff reads an explicit `five_prime_UTR`, it uses it to set the coding start — the new start codon would be right after the 5′ UTR ends. So SnpEff treats everything from the true start codon (1000) up to 20577 as untranslated and begins translation at **20578**, in the wrong frame. In this way, the mutation would result in missense and multiple stop codons thus the warning. 

The coordinates also confirm it. Counting the variant (genomic position 23957) from the bogus coding start at 20578:

```
20578–20731 (154) + 20967–21047 (81) + 21373–21495 (123)
+ 22568–22738 (171) + 22819–22953 (135) + 57 bases into 23901–23996
= 721  →  codon 241
```

That is precisely the `c.721A>G | p.His241Asp` of entry 1 — computed against the broken frame. Counting the *same* variant from the correct start codon (1000), the way `.t2` does, gives `c.1803A>G`, codon 601, at the wobble (3rd) position — a synonymous A→G that leaves Val unchanged. That is entry 2.

**Mystery solved.** 

This misplaced-UTR problem is not a one-off either; I found hundreds of them in my gtf disrupting gene models the same way. 

## Where the bad UTR comes from — and the fix

As suggested by the source column in the gtf, the UTRs were added by `stringtie2utr.py` (a BRAKER helper that decorates a gene model with UTRs inferred from a StringTie assembly). This itself is a long story. In theory we should be able to do UTR prediction in AUGUSTUS with the option `--UTR=on`. However it will return with error and it is a [known issue](https://github.com/Gaius-Augustus/BRAKER/issues/638) in BRAKER3. Katherine the author suggested us to use `stringtie2utr.py` as a workaround. The flaw is in how it builds the UTRs. First, `merge_features` adds a StringTie exon to the gene whenever that exon overlaps a BRAKER CDS and is *not shorter* than it — so a StringTie exon a few bases **longer** than the coding exon it covers (one that pokes into the flanking intron) gets merged in. Then `compute_utr_features` walks each exon and, for the single CDS segment it overlaps, carves whatever sticks out into a UTR:

```python
cds_start = int(overlapping_cds[0]…[3])   # the LOCAL overlapping CDS, not the gene's coding start
if strand == "+":
    if start < cds_start:                  # exon pokes out 5' of THAT CDS segment
        utr5 = … "five_prime_UTR" …
        utr5 = utr5.replace(str(end), str(cds_start - 1))
    if end > cds_end:                      # … or 3' of it
        utr3 = … "three_prime_UTR" …
```

The comparison is purely *local* — an exon against the one CDS it happens to overlap — with no check that the exon is the transcript's first (or last) one, nor if the resulting UTR falls outside the gene's overall coding span as it should. 

Apparently there is now [BRAKER4](https://github.com/Gaius-Augustus/BRAKER4), but unfortunately it seems like the UTR prediction is still done through [`stringtie2utr.py`](https://github.com/Gaius-Augustus/BRAKER4/blob/main/scripts/stringtie2utr.py). While improved on many fronts, `merge_features` still merges over-long StringTie exons, and the UTR carving still uses only local exon-to-CDS comparison — so the root cause is unchanged. This is actually intentional as the author mentioned in one of the related issues that since the gene structure is ab initio, why should we trust it over RNAseq evidence? Btw I just came across this deep learning gene prediction tool called [Helixer](https://www.nature.com/articles/s41592-025-02939-1) that may be worth trying. 

## The fix

So now we need to fix two things:

1. UTRs that fall within coding regions.
2. Bare gene/transcript ID.

We can write a post-processing script to fix both. See [`postprocess_braker_gtf.py`](https://github.com/fanhuan/script/blob/master/postprocess_braker_gtf.py) as an example. Or we can fix the UTR part in `string2utr.py`, see [an updated version](https://github.com/fanhuan/script/blob/master/stringtie2utr.py) in my git repo. Along the way I found a third problem from the raw braker output. There is always an redundant `mRNA` line for each `transcript` that was generated from GeneMark, and it does not include the UTRs that were predicted. `postprocess_braker_gtf.py` will simply remove those lines.

## Do we have to redo the whole annotation? No.

A reasonable worry at this point is that fixing the GTF means re-running everything from scratch. It doesn't. All three cleanups touch only the `gene`, `transcript`, `mRNA`, and `UTR` lines — they **never modify a `CDS` feature** therefore the cds and proteins are unchanged, thus the annotation. However we do need to update the SnpEff database and re-run the annotation of variants.
