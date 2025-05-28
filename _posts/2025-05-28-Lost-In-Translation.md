---
layout: post
title: Lost In Translation
categories: [notes]
tags: [bioinformatics]
---

While working with a vcf file, I noticed that one of the variant looked like this:

ID 			    REF 		ALT

chr1_254_A_T	T           A

I was pretty confused. The ID suggested that A is the REF call and T is the Alternative. However the REF and ALT columns suggest the opposite. I was immediate alarmed since this could have cause problematic genotype calls where 0/0 and 1/1 are switched.

How could this be? I checked my original vcf file with which the current one is a subset of, things are OK. the ID is still chr1_254_A_T, and the REF is A and ALT is T. So where did thing go wrong?

Looking through my notes, I realized that I have converted my vcf to plink format, did some prunning there, and then converted the plink files back to vcf. Could this be the problem?

The variant information is stored in the .bim file, and here is its [definition](https://www.cog-genomics.org/plink/1.9/formats#bim) 

```
.bim (PLINK extended MAP file)
Extended variant information file accompanying a .bed binary genotype table. (--make-just-bim can be used to update just this file.)

A text file with no header line, and one line per variant with the following six fields:

Chromosome code (either an integer, or 'X'/'Y'/'XY'/'MT'; '0' indicates unknown) or name
Variant identifier
Position in morgans or centimorgans (safe to use dummy value of '0')
Base-pair coordinate (1-based; limited to 231-2)
Allele 1 (corresponding to clear bits in .bed; usually minor)
Allele 2 (corresponding to set bits in .bed; usually major)
```

As you can see, column 5 is the minor allele and column 6 is the major. This means we have lost which one is REF and which one is ALT. When you use `plink --recode vcf` to convert your .bim back to vcf, it will just assume that the major is REF, which is not always true. 

So what can you do? When converting your vcf to the plink format via `plint --vcf input.vcf --make-bed`, make sure to add either `--keep-allele-order` or `--real-ref-alleles`. Then the .bim file will be correct and when you convert it back to vcf later, there should be any problem. It is said that from plink 2.0 does not have this problem and will always respect the original REF/ALT order.

Happy genotyping!


