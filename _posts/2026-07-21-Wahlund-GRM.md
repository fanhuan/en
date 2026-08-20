---
layout: post
title: When a GRM Says Cousins Are Clones — Wahlund Meets Rare-Allele Weighting
categories: [notes]
tags: [GWAS]
---

In the [last post](https://fanhuan.github.io/en/2026/06/23/fastGWA/) I talked myself into fastGWA and its **sparse GRM**: compute all the pairwise relationships, then zero out everything below a cutoff (default 0.05) so only close kin survive as a random effect, while PCs handle population structure. Clean division of labour. So I went and built the thing. And the moment I looked at it, it was obviously broken — in a way that turned out to be the same lesson as my [founders post](https://fanhuan.github.io/en/2026/05/15/Plink-Founders/), wearing a different costume.

This post is about why my GRM reported pairs of individuals as being **more than identical twins**, and why the culprit was not a bug but a property of pooling divergent populations into one relationship matrix — the **Wahlund effect**, amplified by how GCTA weights variants.

## 1. The symptom: a "sparse" GRM that wasn't sparse

I built the GRM from my LD-pruned, `--maf 0.01` set (~425k SNPs, ~2000 oil palm samples) and sparsified at the default 0.05:

```bash
gcta --bfile pruned_set --make-grm --out grm_full --threads 20
gcta --grm grm_full --make-bK-sparse 0.05 --out grm_sparse --threads 20
```

The log cheerfully reported **922,287 pairs** retained. For ~2000 individuals there are only ~2 million possible pairs — so **~45% of all pairs survived a "keep only close relatives" cutoff.** A sparse GRM that keeps half the matrix is not sparse, and no cohort has half its pairs related at 0.05+.

So I dumped the full GRM (`--make-grm-gz`) and actually looked at the numbers. The GCTA `.grm.gz` is four columns — `i, j, #SNPs, value` — with `i==j` being the diagonal. Two things fell out:

- **Diagonals** should be ~1 (a diagonal is `1 + F`, so 1 plus the inbreeding coefficient). Mine had a **mean of 1.35 and a maximum of 5.96**. A diagonal of 6 implies `F ≈ 5`. Inbreeding coefficients live in [0, 1]. This is not a number biology can produce.
- **Off-diagonals** (the actual relatedness values) topped out at **4.5**, with **8,820 pairs reporting relatedness above 1.0**. Relatedness caps at ~1 for clones/MZ twins and ~0.5 for parent–offspring. My matrix was calling thousands of pairs *five times more related than identical twins*.

Those impossible values are the tell. This isn't "my population is unusually related." It's a distorted matrix.

## 2. The fingerprint: sort the diagonal by population

The clue was in *who* had the crazy diagonals. My dataset is a germplasm collection: a big **breeding population** coded by parent numbers (`081.081`, `161.161`, `TS1`, `TS3` …) plus a scatter of named **exotic origins** (Angola, Deli, Ghana, Nigeria, Tanzania, AVROS, Ekona…). When I averaged the GRM diagonal within each group, it split into two clean tiers:

| Group | mean diagonal | n |
|---|---|---|
| Tanzania | 4.52 | 14 |
| AVROS | 3.95 | 3 |
| Nigeria | 3.53 | 19 |
| AGO (Angola) | 3.28 | 115 |
| Ghana | 2.59 | 22 |
| Deli | 2.16 | 82 |
| … | … | … |
| **TS3** | **1.08** | 170 |
| **081.081** | **0.83** | 191 |
| **161.161** | **0.80** | 196 |
| **TS1** | **0.53** | 61 |

The exotic origins — the small, genetically distinct groups — are the ones blowing up. The large breeding population sits right around a sane ~1 (or even below). The inflation isn't random noise or bad samples; it's **structured by population membership**. That points at one thing: allele frequencies.

## 3. Why rare-in-the-pool alleles detonate the GRM

Here is GCTA's GRM entry between individuals *j* and *k*, summed over variants *i* with pooled allele frequency `p_i`:

```
A_jk = (1/M) Σ_i  (x_ij − 2p_i)(x_ik − 2p_i) / [ 2 p_i (1 − p_i) ]
```

Stare at the denominator: **`2 p_i (1 − p_i)`**. When a variant is rare in the pooled sample, `p_i` is tiny, so the denominator is tiny, so every term for that variant is **divided by a very small number** — its contribution explodes. This is deliberate: rare-variant sharing *is* stronger evidence of recent common ancestry, so GCTA up-weights it. The weighting is only trustworthy, though, when `p_i` actually describes the individuals you're applying it to.

Now bring in the structure. GCTA computes each `p_i` **across the whole pooled sample**, which is ~75% breeding population. Take a variant that is nearly absent in the breeding majority but **common, even fixed, inside Tanzania**. Pooled, its `p_i` comes out small. But every Tanzanian is homozygous for that "rare" allele — so `(x − 2p)` is large *and* it gets divided by a tiny `2p(1−p)`. Two Tanzanians both homozygous for it contribute a gigantic positive product to their pairwise `A_jk`, and each contributes a gigantic term to their own diagonal. Multiply across all such variants and you get diagonals of 4–6 and within-origin "relatedness" above 1.

The individuals aren't clones. The **frequencies are computed on the wrong reference population**, and the inverse-frequency weighting turns that mismatch into enormous numbers.

## 4. The name for it: the Wahlund effect

There's a classic population-genetics name for the root cause. The **Wahlund effect**: when you pool subpopulations that have different allele frequencies and treat them as one HWE population, you see a **deficit of heterozygotes / excess of homozygotes** relative to what pooled allele frequencies predict — even if every subpopulation is in perfect HWE internally. Structure masquerades as inbreeding.

A GRM diagonal is essentially a measurement of an individual's homozygosity *relative to pooled-HWE expectation*. So the Wahlund excess-homozygosity lands straight on the diagonal as spurious inbreeding, and the same frequency mismatch lands on the off-diagonals as spurious within-group relatedness. My "`F ≈ 5`" individuals aren't inbred — they belong to a subpopulation whose allele frequencies look nothing like the pool I forced them into.

It's worth noticing this is the *second* way I've been burned by fake inbreeding. Back in [How Low is Low?](https://fanhuan.github.io/en/2025/04/16/Sequencing-Coverage/) the villain was low **coverage**: miss the second allele of a heterozygote and you call it homozygous, so observed heterozygosity (H-obs) sags and F inflates as a *sequencing artifact*. Here the villain is population **structure**: pool subpopulations and the heterozygote deficit inflates F as a *base-population artifact*. Same symptom — excess homozygosity, an inbreeding coefficient that's too high — two completely unrelated causes. When F looks wrong, "is it the reads or is it the base population?" is now a question I know to ask.

This is the exact same lever as my [founders / base-population](https://fanhuan.github.io/en/2026/05/15/Plink-Founders/) headache, just downstream. There the question was *whose allele frequencies define `--maf`*; here it's *whose allele frequencies define the GRM weights*. Same denominator problem, different tool. The base population you (implicitly) choose is doing all the work.

## 5. Two things that did *not* fix it

**Adding samples back.** I first ran this with 1,996 samples, then noticed I'd expected ~2,023 and regenerated with 2,022. The distortion was *identical* (diag max 5.96, off-diag max 4.5, ~45% surviving the cutoff either way). Of course it was — the problem was never the sample count, it was which populations were pooled.

**Tuning the sparse cutoff.** The instinct is to raise `--make-bK-sparse` from 0.05 to 0.1 or 0.2 to trim the flood of pairs. But raising the cutoff on a *distorted* matrix just discards a different arbitrary slice of a matrix whose numbers are wrong. It treats the symptom (too many surviving pairs) and ignores the disease (the values themselves are inflated by frequency mismatch). It would also throw the PCA off the same cliff — and sure enough, this is exactly why my PC1 eigenvalue (585) dwarfed PC2 (222): PC1 was separating the exotic origins from the breeding population, i.e. it was reporting the very structure the GRM was choking on.

## 6. The twist: the "broken" matrix still did one job

Here's where I nearly made things worse. Having seen diagonals of 6 and relatedness above 1, my gut said *this matrix is garbage, throw it out* — I even talked myself into dropping the GRM from a downstream family-based mixed model entirely.

That was wrong, and my own earlier notes said so. When I fed this *same* distorted GRM into the mixed model as a variance component, it **controlled genomic inflation just fine** — the direct-effect λ came out around 0.95. Drop the GRM instead, and λ blew up past 6.

Why does a matrix full of impossible numbers still work? Because a mixed model doesn't lean on the GRM's *magnitudes* — it leans on its *block structure*: who clusters with whom. And the block structure is **correct**. The rare-allele weighting inflated the *sizes* of the within-origin similarities, but it didn't scramble *which* individuals are similar — Tanzanians still look like Tanzanians. So as a device for saying "don't be surprised these individuals' phenotypes resemble each other," the distorted GRM is still telling the truth.

What the distortion *does* wreck is anything that reads the magnitudes literally:

- **Heritability / variance components** — with diagonals inflated, the model mis-partitions variance (in one trait my family-relatedness term collapsed to exactly zero while the GRM ate 43%). Any h² from this is fiction.
- **"Is this pair related?"** — the off-diagonals are uninterpretable as kinship.
- **Per-SNP stability** — a fraction of SNPs blow up (tiny/huge effective N) and need filtering.

So "broken" was too strong. The right question isn't *is this GRM valid?* — it's *valid for what?*

## 7. So what you do depends on what you need it for

- **If you need the magnitudes** — heritability, kinship, "how related are these two" — the matrix is unusable as-is, and fixing it is a **design** decision, not a flag. Stop pooling: restrict to the intended study population (my breeding-cross groups, sane ~1 diagonals) and rebuild, or go **within-population / stratified** and meta-analyze, so `2p(1−p)` means what GCTA thinks it means. No `--maf`, cutoff, or sample juggling fixes a matrix that pooled populations it shouldn't have.
- **If you only need structure control** in a mixed model, the distorted GRM may still calibrate — but **prove it with the QQ plot / λ**, don't assume. The tempting cleanup — rebuild the GRM on common variants (MAF ≥ 0.05) to tame the diagonals — *seems* principled, but for family data it can backfire badly. Test it before you trust it; here's what happened when I did.

## 8. The cleanup that backfired

The obvious next move — the one I'd half-talked myself into — is to rebuild the GRM from **common variants only (MAF ≥ 0.05)**. The logic feels airtight: the explosion came from rare alleles hitting the tiny `2p(1−p)` denominator, so drop the rare alleles and the diagonals should settle back to ~1. I built it and ran it. It failed twice over.

**First, it didn't even clean the matrix.** The extreme spikes came down (max diagonal 6 → 2.5), but the *baseline* barely moved — mean diagonal 1.35 → 1.27, still thousands of impossible off-diagonals, still ~43% of pairs above the cutoff. By now the inflation isn't coming from *rare* alleles; it's coming from **differentiated *common* alleles** — an allele at 8% pooled but 40% inside one origin sails through a MAF ≥ 0.05 filter and carries the full Wahlund signal. You can't threshold away structure that lives in the common variants.

**Second — and worse — it broke the analysis.** Feeding the common-only GRM into the mixed model, the variance-component fit blew up with *"Factor is exactly singular"* for exactly my well-measured traits — the ones that had fit fine with the rare-variant GRM. The reason is the lesson I won't forget: **in family data, rare variants are what distinguish siblings.** My families are huge full-sib crosses (100–200 sibs each). Strip the rare variants and the remaining common-only genotypes make those sibs look nearly identical — near-duplicate rows in the GRM — so the matrix goes rank-deficient and the model can't invert it. The "noise" I was filtering out was carrying the information that told relatives apart.

So the intuitive fix was worse than the disease: it left the structural distortion in place *and* destroyed the within-family signal. I reverted to the rare-variant-inclusive GRM — impossible diagonals and all — and everything converged again. **The ugly matrix was the working one.**

## 9. Key takeaway

Before you trust a GRM — sparse or dense — **look at the numbers, not just the log line**:

```bash
gcta --grm grm_full --make-grm-gz --out check
# diagonals should sit near 1; off-diagonals near 0 with a thin
# right tail at ~0.125 / 0.25 / 0.5. Anything above 1 is impossible.
zcat check.grm.gz | awk '$1==$2{d[$4>1.25]++} $1!=$2 && $4>1{imposs++}
  END{print "diag>1.25:", d[1]+0, " off-diag>1:", imposs+0}'
```

If diagonals run well above 1 and off-diagonals climb past 1, you're seeing the **Wahlund effect refracted through GCTA's inverse-frequency weighting** — a fancy way of saying *your allele frequencies were computed on the wrong population.* But don't over-correct the way I almost did: a matrix like this is worthless as *kinship or heritability*, yet can still earn its keep as *structure control* in a mixed model, because block structure survives what magnitude doesn't. So don't ask "is my GRM broken?" — ask "**broken for which job?**", and confirm the answer with λ.

And resist the reflexive cleanup. Filtering to common variants *felt* like the principled fix and made things strictly worse — it left the real (common-allele) structure untouched and threw away the rare variants that tell siblings apart, so the GRM went singular. Twice now the tidy-looking move (drop the GRM; drop the rare variants) was the wrong one, and the ugly, rare-variant-inclusive, impossible-on-paper matrix was the one that actually worked. The GRM, like `--maf`, like PCA, is only as meaningful as the base population you feed it — so choose that population on purpose, know which of its numbers you're leaning on, and check what a "cleanup" is quietly throwing away before you trust it.
