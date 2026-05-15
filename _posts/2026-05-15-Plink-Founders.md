---
layout: post
title: The Hidden Importance of Founders in PLINK Analysis
categories: [notes]
tags: [GWAS]
---

Recently I starting doing family-based GWAS using [SNIPAR](https://github.com/AlexTISYoung/snipar). This means I need to know the relationship between the samples in my analysis. Previously I only have info on two families which takes the majority of the data that I am working on, and I just treated the rest as un-related. But I know that is not true. In order to increase the sample size, I used [KING](https://www.kingrelatedness.com/), a kinship inference tool to predict the possible relationships based on SNP data. Then I check with the breeders to see whether they agree with those relationships. So now in my dataset, a lot of individuals have derived hypothetical PID or MID (parental or maternal ID), just to suggest full or half sibling relationships.

Then I just went ahead to do my usually data preparation using [PLINK](https://www.cog-genomics.org/plink/) until I realized some problem, and it centers around this concept called __founder__.

## 1. What is a "founder" in PLINK?

- Anyone with `0 0` in columns 3–4 of the `.fam` file (no parents listed)
- **Not** a biological concept — purely a pedigree bookkeeping artifact
- Population datasets with no pedigree: everyone is a founder (fine)
- Breeding/family datasets with pedigrees filled in: only the top generation are founders (can be very few)

## 2. Why PLINK cares: the statistical rationale

This is all because by default, PLINK calculates allele frequencies based on __founders only__. Related individuals share alleles IBD — counting them equally inflates the effective sample size and biases allele frequency estimates. Using only founders approximates sampling independent chromosomes from the base population.

We talked about [base population](https://fanhuan.github.io/en/2025/02/19/Base-Population/) before. At that point, I thought it only affects certain plink functions such as `--maf` or `--hwe`. Not until today did I realized that by default, any feature of PLINK is based on the base population or the founders. OK so the first conclusion of today is, in PLINK, __founders are the base population__. 

## 3. Analyses silently affected by founder status

Basically any analysis. You need to be very careful about whether you want to just use the founders (if your pedigree in the `.fam` file is correct), or all the individuals (turn on `--nonfounders`). Sometimes you also do not want to do the latter if your dataset is heavily biased by some families like I do. Here is a limited summary table for features I usually use. But again, only founders are used for allele freq calculation by default for any featuer, any!

| Flag | What uses founders | Consequence if few founders |
|---|---|---|
| `--freq` | Frequency computed from founders only | Inaccurate MAF |
| `--maf` | Filters based on founder frequencies | Wrong variants removed/retained |
| `--hwe` | HWE test on founders only | Underpowered or wrong results |
| `--pca` (PLINK 1.9) | GRM built from founders only | Fails if N_founders < 20 or has duplicates |
| `--pca approx` (PLINK 2) | Allele freqs from founders | Hard error if N_founders < 50 |
| `--indep-pairwise` / LD pruning | r² computed from founders only | Over-pruning when few founders (spurious LD from small N) |
| `--genome` / IBD | Uses founder allele frequencies | Biased IBD estimates |

## 4. How did I discover this silent scary behavior?

Like I said in the beginning, after adding all those PID and MID, there are very few founders left in my dataset, and I noticed that a lot more SNPs were filtered out under the same `--maf`. Also, PCA failed:

- **PLINK 1.9 `--pca`**: silent failure with cryptic GRM error ("Failed to extract eigenvector(s) from GRM")
- **PLINK 2 `--pca approx`**: explicit error ("less than 50 founders available to impute allele frequencies")

Both errors have the same root cause: the GRM and allele frequency estimation are operating on fewer than 50 individuals for a dataset with thousands of samples. Then I realized that it also affects the LD prunning. 

The same dataset also revealed a cascade of problems with LD pruning (`--indep-pairwise`), which is a prerequisite for PCA:

**Attempt 1 — default (27 founders):** retained only ~4.5% of variants vs ~12.4% for a comparable dataset with 339 founders. Noisy r² from small N causes spurious high-LD calls and over-pruning.

**Attempt 2 — `--nonfounders` (all 1996 individuals):** retained even fewer variants (~4.2%). Fix: relatedness inflates r² — full siblings and clones share long IBD haplotypes, making unlinked variants appear correlated within the pruning window.

**Attempt 3 — `--rel-cutoff 0.125` to get unrelated subset first:** 1982 of 1996 individuals excluded, leaving 14 — worse than the original 27 founders. Fix: in a pedigree/breeding dataset, 2nd-degree relatedness is ubiquitous; a 0.125 cutoff removes almost everyone.

**Attempt 4 — `--rel-cutoff 0.25` (remove only 1st-degree + duplicates):** 489 individuals remaining — workable. But running `--indep-pairwise` on this subset failed with "less than two founders" because all 489 had parents listed in the `.fam` file that were no longer in the dataset.

**Attempt 5 — add `--make-founders`:** promotes all individuals with absent parents to founder status. This is necessary whenever you use `--keep` to subset a pedigree dataset. Still retained fewer variants than expected (~3.1%), because population structure (many divergent populations) inflates within-window r² regardless of relatedness.

**Validation:** despite all this, PCA eigenvectors computed before and after LD pruning showed >0.99 correlation — confirming that for PCA, the exact pruning strategy matters little in practice.

## 5. Solutions and tradeoffs

- **`--nonfounders`**: include all individuals — appropriate when you want population-level statistics despite pedigree structure
- **`--freq` + `--read-freq`**: pre-compute frequencies from a reference panel or a manually curated unrelated subset, then feed them in — most principled for mixed datasets
- **Remove relatives first for LD pruning**: use `--rel-cutoff` (can try third degree:0.125 or second degree: 0.25) + `--make-founders` (required when parents are absent from the kept subset) + `--indep-pairwise`; apply the resulting prune list to the full dataset. For highly structured multi-population datasets, population structure will still inflate LD — per-population pruning followed by taking the union of kept variants is the most principled approach.
- **`--bad-freqs`**: override (not recommended — hides the problem)

## 6. Key takeaway

Always check your founder count before running any frequency-dependent analysis:

```bash
grep "founders" your.log
```

If you have a pedigree-filled `.fam` file and few founders, every downstream result is quietly wrong unless you intervene. The `--hwe` case is worth special attention: HWE violations are *expected* in related samples, so filtering on HWE in a pedigree dataset silently removes valid markers.
