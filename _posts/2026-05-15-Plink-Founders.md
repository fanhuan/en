---
layout: post
title: The Hidden Importance of Founders in PLINK Analysis
categories: [notes]
tags: [GWAS]
---

Recently I starting doing family-based GWAS using [SNIPAR](https://github.com/AlexTISYoung/snipar). This means I need to know the relationship between the samples in my analysis. Previously I only have info on two families which makes the majority of the data that I am working on, and I just treated the rest as un-related. But I know that is not true. In order to increase the sample size, I used [KING](https://www.kingrelatedness.com/), a kinship inference tool to predict the possible relationships based on SNP data. Then I check with the breeders to see whether they agree with those relationships. So now in my dataset, a lot of individuals have derived hypothetical PID or MID (parental or maternal ID), just to suggest their full or half sibling relationships.

Then I just went ahead to do my usually data preparation using [PLINK](https://www.cog-genomics.org/plink/) until I realized some problem, and it centers around this concept called __founder__.

## 1. What is a "founder" in PLINK?

It is basically anyone with `0 0` in the PID (column 3) and MID (columns 4) of the `.fam` file. Meaning, we do not have information on who their parents are. Thus they are founders themselves. Since they might not be the actual founders from their population, therefore it is **Not** a biological concept but purely a pedigree bookkeeping artifact. If there is no pedigree info in the whole dataset, then everyone becomes a founder. In our family data where we have grandparents, F1 and F2, only the grandparents are founders. 

## 2. Why founder matters

By default, `PLINK` calculates allele frequencies based on __founders only__. Meaning, if we do a `--maf 0.05` filtering, say for variant chr1_10000_A_T, in the founders it is all A, but maybe there are a lot of copies of T in non_founders, this variant will still be considered not meeting the maf cutoff and filtered out. This makes sense when we do have the parents or grandparents in the dataset, since mendelianly they should have all the alleles of their offsprings. But in my dataset, due to the include of hypothetical PID and MID, it would be a huge lose if only "founders" are considered. Using only founders approximates sampling independent chromosomes from the base population.

Beyond presence and absense of alleles, this is also related to how allele frequencies in this population should be calculated. Allele frequency estimation assumes you've drawn N independent chromosomes from the population. Since related individuals share alleles IBD, they are not independent observations. If you genotype a parent and then genotype their three children, you're partly re-counting the parent's alleles three more times — the children's genotypes are predictable from the parent's. The "effective sample size" is the count of independent draws, which is far smaller than the raw count. Using raw count makes you think your estimate is more precise than it is, and it lets a few large families dominate. This concept is realted to [base population](https://fanhuan.github.io/en/2025/02/19/Base-Population/) that we talked about before. So founders makes the base population.

At that point, I thought it only affects certain plink functions such as `--maf` or `--hwe`. Not until today did I realized that by default, any feature of PLINK is based on the base population or the founders. OK so the first conclusion of today is, in PLINK, __founders are the base population__. 

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

1. Like I said in the beginning, after adding all those PID and MID, there are very few founders left in my dataset, and I noticed that a lot more SNPs were filtered out under the same `--maf`. 

2. Then I realized that it also affects the LD prunning because under the same parameters (`--indep-pairwise 500 50 0.8 `), higher percentage of SNPs were found in LD/heavier prunning. 

3. Eventually, PCA failed:

- **PLINK 1.9 `--pca`**: silent failure with cryptic GRM error ("Failed to extract eigenvector(s) from GRM"), probably a singularity problem.
- **PLINK 2 `--pca approx`**: explicit error ("less than 50 founders available to impute allele frequencies")

Both errors have the same root cause: the GRM and allele frequency estimation are operating on fewer than 50 individuals for a dataset with thousands of samples. 

## 5. Solutions and tradeoffs

**`--nonfounders`**: Usually this is an easy problem to fix by turning on this option and use all individuals in the dataset. This indeed retained slightly more SNPs (less than 10%) using all thousands of individuals, however still significantly less than the previous batch with only hundreds of individuals. 


- **`--freq` + `--read-freq`**: pre-compute frequencies from a representative subset, then feed them in — most principled for mixed datasets. Four-step workflow:
  1. Pre-filter without `--maf` (apply `--geno` and `--mind` only)
  2. Define a representative subset: include all true founders (PID=0, MID=0) plus **one individual per unique (PID, MID) pair** among non-founders. This ensures every independent lineage contributes exactly once — full siblings collapse to one representative, but half-siblings (who share only one parent and thus have *different* (PID, MID) combinations) each get their own representative.
  3. Compute frequencies from that subset: `plink --bfile ... --keep <subset> --freq --nonfounders --out ...` — **`--nonfounders` is required here** because the subset includes non-founders (e.g., the half-sib representatives); without it PLINK falls back to the 27 true founders.
  4. Apply MAF filter using the pre-computed frequencies: `plink --bfile ... --read-freq <freq_file> --maf 0.005 --make-bed --out ...`

- **Remove relatives first for LD pruning**: use `--rel-cutoff` (can try third degree: 0.125 or second degree: 0.25) + `--make-founders` (required when parents are absent from the kept subset) + `--indep-pairwise`; apply the resulting prune list to the full dataset. For highly structured multi-population datasets, population structure will still inflate LD — per-population pruning followed by taking the union of kept variants is the most principled approach.

  **A note on consistency between MAF and LD representative selection:** It is natural — and correct — to use different criteria at the two stages. For MAF estimation, the pedigree-based approach (one per unique PID/MID pair) is optimal because it uses known family structure to ensure independent lineage representation; half-siblings are included because their distinct (PID, MID) pairs represent genuinely different crosses. For LD estimation, a kinship cutoff (e.g., 0.125) uses empirical relatedness to prevent shared haplotype blocks from inflating apparent LD; half-siblings (IBD ≈ 0.25) are excluded by this threshold. The LD stage being *stricter* about relatedness than the MAF stage is the safe direction and is not a methodological inconsistency.
- **`--bad-freqs`**: override (not recommended — hides the problem)


**Attempt 1 — default (a couple dozens of founders):** retained only ~4.5% of variants vs ~12.4% for a previous version where we assigned hundreds of founders. Noisy r² from small N causes spurious high-LD calls and over-pruning.

**Attempt 2 — `--nonfounders` (all individuals in thousands):** retained even fewer variants (~4.2%). This is counterintuitive — more individuals, yet worse results. The explanation requires understanding two distinct sources of r² inflation:

- **Attempt 1** suffers from **small-N noise**: with only ~27 individuals, r² estimates are imprecise and systematically upward-biased (r² is bounded at 0, so random errors can only push it higher, never lower). Some truly unlinked variants get flagged as in LD by chance.

- **Attempt 2** suffers from **kinship-induced pseudo-LD**: related individuals share long IBD haplotype blocks. Two variants sitting on the same shared haplotype will co-occur systematically across all members of a family — not because of actual LD in the population, but because of shared ancestry. Within a pruning window, PLINK cannot distinguish this from real LD and prunes accordingly. This is especially bad when you have a lot of related samples in your dataset.

In my case, the kinship inflation turns out to be larger than the small-N noise inflation, so going from a couple of dozens of founders to thousands of related individuals makes things worse. Therefore we need to remove relatives first — you need a dataset where r² reflects actual population LD, not shared ancestry.

At first I tried to get a unrelated subset using `--rel-cutoff 0.125` , but again only a couple of dozens of individuals are left. leaving 14 — worse than the original 27 founders. This is because 2nd-degree relatedness is pretty common in my dataset. Then I tried a lower cut off `--rel-cutoff 0.25` (remove only 1st-degree + duplicates), now we have a few hundreds remaining. You then need to make all of them founders (`--make-founders`)

**Attempt 5 — add `--make-founders`:** promotes all individuals with absent parents to founder status. This is necessary whenever you use `--keep` to subset a pedigree dataset. Still retained fewer variants than expected (~3.1%), because population structure (many divergent populations) inflates within-window r² regardless of relatedness.

**Validation:** despite all this, PCA eigenvectors computed before and after LD pruning showed >0.99 correlation — confirming that for PCA, the exact pruning strategy matters little in practice.


## 6. Key takeaway

Always check your founder count before running any frequency-dependent analysis:

```bash
grep "founders" your.log
```

If you have a pedigree-filled `.fam` file and few founders, every downstream result is quietly wrong unless you intervene. The `--hwe` case is worth special attention: HWE violations are *expected* in related samples, so filtering on HWE in a pedigree dataset silently removes valid markers.
