---
layout: post
title: Order of Things
categories: [notes]
tags: [bioinformatics]
---

I was doing some QC of variants called using `bcftools filter`. Here are the three versions I had so far.

# First Version

```
bcftools filter -g3 -G10 | bcftools view -i 'QUAL>20 & MAF>0.0000001' -m2 -M2
```

Firstly let's explain each option separately.

- `-g3`: Filter SNPs within 3 base pairs of an indel (the default) or any combination of indel,mnp,bnd,other,overlap. This is because a SNP this close to an indel might be incorportated into this indel as another allele.
- `-G10`: Filter clusters of indels separated by 10 or fewer base pairs allowing only one to pass. Because one of the indels might be a result of the other. They should be considered together.
- `QUAL>20`: this is the QUAL column of the vcf file. It is phred-scaled, and the higher the better. QUAL = 20 means only 1% of the chance that this variant is false.
- `MAF>0.00000001`: Minor allele frequency for filtering rare variants. But here the number I set is so low, as long as you have 1 allele count, you will stay. So basically this is filtering for monomorphic/invariant calls. 
- `-m2`: minimum 2 alleles
- `-M2`: maximum 2 alleles. So only bi-allelaic stays.

The problem with this version is that along the way, I lost a SNP because it was close to an indel that had low QUAL. Because `-g3 -G10` goes first, I filtered that SNP with high QUAL for an indel with low QUAL. Therefore I reordered things in the next version.

# Second Version

```
bcftools view -i 'QUAL>20 & MAF>0.0000001' -m2 -M2 | bcftools filter -g3 -G10
```

One obvious thing to improve is that MAF>0.0000001 the misleading. Might as well use 0 instead. 

Now the problem becomes, should we filter the multiallellic (`-m2 -M2`) and monomorphic (MAF>0) variants upfront? For multiallellic indels, they might be true and with high quality scores, and they could be used in `-g3` and `-G10`, and be discarded later. For the monomorphic indels, it could be everyone 1/1, monomorphic for the ALT — a fixed difference from the reference. This could mean that there is a mistake in the assembly and the indel is still true. I actually do not have a strong preference of whether this should go earlier or later. How do you think?

# Third Version

```
bcftools view -i 'QUAL>20' -m2 -M2 | bcftools filter -g3 -G10 | bcftools view -i `MAF>0` -m2 -M2
```

What would you have done differently?
