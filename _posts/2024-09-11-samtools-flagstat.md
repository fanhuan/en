---
layout: post
title: Inferring Assembly Quality From Mapping Stats
categories: [data analysis]
tags: [bioinformatics]
---

I'd like to compare two assemblies. Besides the normal things such as N50, N90, L50 etc, I'd like to see how my reads mapped to them, especially where they are properly mapped. I am using bwa, but for some reason it does not give me a good stats on the mapping as `hisat2` does. Therefore I have to take an extra step, using `samtools flagstat` to evaluate the resulting bams. The output looks like this (real numbers from a bam I have):

```
266617707 + 0 in total (QC-passed reads + QC-failed reads)
264440370 + 0 primary
0 + 0 secondary
2177337 + 0 supplementary
33866976 + 0 duplicates
33866976 + 0 primary duplicates
263617427 + 0 mapped (98.87% : N/A)
261440090 + 0 primary mapped (98.87% : N/A)
264440370 + 0 paired in sequencing
132220185 + 0 read1
132220185 + 0 read2
242609574 + 0 properly paired (91.74% : N/A)
260859502 + 0 with itself and mate mapped
580588 + 0 singletons (0.22% : N/A)
16536568 + 0 with mate mapped to a different chr
8566316 + 0 with mate mapped to a different chr (mapQ>=5)
```



### a. **266617707 + 0 in total (QC-passed reads + QC-failed reads)**

- This is the number of records in the bam file examined and it is higher than the number of reads I gave `bwa`, which is 264440370. This is the sum of primary(264440370), secondary(0) and supplementary(2177337) reads. The `+ 0` means no reads failed quality control checks (QC-failed reads). 

### b. **264440370 + 0 primary**

- 264440370 reads are considered primary alignments, meaning they represent the best or only alignment for a read. In this case every read have a primary alignment. 0 here is still for QC-failed ones.

### c. **0 + 0 secondary**

- This indicates that there are no secondary alignments, meaning no reads aligned to multiple locations (which can happen in regions of the genome with repetitive sequences).

### d. **2177337 + 0 supplementary**

- These are partial or chimeric alignments, where a read is split across different regions. This often happens with structural variants or when parts of the read align to different locations.

### e. **33866976 + 0 duplicates**

- Those alignments are marked as duplicates. Duplicates are usually caused by PCR amplification during library preparation and are flagged because they represent the same original DNA fragment.

### f. **33866976 + 0 primary duplicates**

- All of the duplicates mentioned above are primary duplicates, meaning they correspond to the main alignment of the reads. This is obvious since there is no secondary alignments overall.

### g. **263617427 + 0 mapped (98.87% : N/A)**

- 263617427 out of 266617707 alignment are mapped to the reference genome, representing 98.87% of the total alignments. This is a very high mapping rate, indicating good quality sequencing data. `N/A` is the result of 0/0 for the QC-failed reads (none).

### h. **261440090 + 0 primary mapped (98.87% : N/A)**

- 261440090 out of the 266617707 alignment are primarily mapped to the reference genome, representing 98.87% of the total alignments. The percentage is the same because only two decimal places were displayed.

### i. **264440370 + 0 paired in sequencing**

- All the reads I gave `bwa` for mapping are paired.

### j. **132220185 + 0 read1**

- Half of them are forward.

### k. **132220185 + 0 read2**

- Half of them are reverse. 

### l. **242609574 + 0 properly paired (91.74% : N/A)**

- 242609574 out of 264440370 reads (91.74%) are properly paired. This means both mates in a pair are mapped correctly with respect to the expected insert size and orientation.

### m. **260859502 + 0 with itself and mate mapped**

- 260859502 reads are paired where both the read and its mate are mapped to the reference. 

### n. **580588 + 0 singletons (0.22% : N/A)**

- 580588 reads are singletons, meaning it mapped to the reference but its mate was not. 

### o. **16536568 + 0 with mate mapped to a different chr**

- 16536568 reads are paired with their mates mapped to a different chromosome. This can indicate structural variations or mapping errors, depending on the context. This number is smaller than m-l since there could be reads with wrong orientation or insert size. 

### p. **8566316 + 0 with mate mapped to a different chr (mapQ>=5)**

- Of the 16536568 reads mapped to different chromosomes, 8566316 (more than half) have a mapping quality score of at least 5. This suggests mapQ >=5 is the minimal quality threshold we should use. Depending on the sequencing depth, perhaps can set to 10 or 20.

  

## Summarized relations:

- a = b + c + d
- b = i (since I only used pair-end reads for mapping)
- j = k = i/2
- m + n = h

# MarkDuplicate

`MarkDuplicate` also gives some metrics about the bam: 

```
LIBRARY: FKDN240184845-1A	
UNPAIRED_READS_EXAMINED: 580588	
READ_PAIRS_EXAMINED: 130429751	
SECONDARY_OR_SUPPLEMENTARY_RDS: 2177337	
UNMAPPED_READS: 3000280	
UNPAIRED_READ_DUPLICATES: 392724	
READ_PAIR_DUPLICATES: 16737126	
READ_PAIR_OPTICAL_DUPLICATES: 13545038	
PERCENT_DUPLICATION: 0.12954	
ESTIMATED_LIBRARY_SIZE: 2100842716
```

- UNPAIRED_READS_EXAMINED: n
- READ_PAIRS_EXAMINED: m/2
- SECONDARY_OR_SUPPLEMENTARY_RDS: c+d
- UNMAPPED_READS: b-m-n
- UNPAIRED_READ_DUPLICATES: reads marked as duplicates in UNPAIRED_READS_EXAMINED.
- READ_PAIR_DUPLICATES: read pairs marked as duplicates in READ_PAIRS_EXAMINED. UNPAIRED_READS_EXAMINED + 2*READ_PAIRS_EXAMINED = e = f
- READ_PAIR_OPTICAL_DUPLICATES: read pairs marked as optical duplicates in READ_PAIRS_EXAMINED. This is a subset of READ_PAIR_DUPLICATES.
- UNPAIRED_READ_DUPLICATES+READ_PAIR_DUPLICATES+READ_PAIR_OPTICAL_DUPLICATES < e. This is because the e is alignment number, not read number.
- PERCENT_DUPLICATION: f/h
- ESTIMATED_LIBRARY_SIZE: estimated genome size.

As you can see, this give us most of the information comparing to `samtools flagstat` except the concordance between two reads (l, o, p). Therefore `samtools flagstat` is still needed. p wouldn't be relavant if we have filtered with mapQ>=5 (`samtools view -b -q 5 in.bam > filtered.bam`).

# Comparing across two references

How to use the stats given by `samtools flagstat` to compare two references?

a. The lower the better

b, i, j & k: are the same since the number of reads used for mapping is the same.

c &d: The lower the better (same as lower a)

e&f. This is a bit indirect but the higher the better, suggesting higher mapping rate. But should just check g&h instead.

g&h: the higher the better.

l: the higher the better.

m: the higher the better

n: the lower the better

o: the lower the better, but is part of g&h

o-p: the lower the better, but is part of o. 

# Conclusion

In sum, four main things to look at: 

1. lower a: lower secondary or supplementary mapping)
2. higher g&h: higher overall mapping rate)
3. higher m: higher paired mapping
4. higher l: higher concordant mapping.

Conveniently the report provides four percentages for us to use: 

-  The higher the better: g h and i
- The lower the better: n.

