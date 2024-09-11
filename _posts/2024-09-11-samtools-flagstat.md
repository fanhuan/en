---
layout: post
title: samtools flagstat
categories: [data analysis]
tags: [bioinformatics]
---


I'd like to compare two assemblies. Besides the normal things such as N50, N90, L50 etc, I'd like to see how my reads mapped to them, especially where they are properly mapped. I am using bwa, but for some reason it does not give me a good stats on the mapping as hisat2 does. Therefore I have to take an extra step, using `samtools flagstat` to evaluate the resulting bams. The output looks like this (real numbers from a bam I have):

### 1. **306,850,015 + 0 in total (QC-passed reads + QC-failed reads)**

- This indicates the total number of reads in the file: 306,850,015. The `+ 0` means no reads failed quality control checks (QC-failed reads).

### 2. **304,481,182 + 0 primary**

- Out of the total reads, 304,481,182 are primary alignments. These are the main alignments for each read, with no secondary alignments recorded.

### 3. **0 + 0 secondary**

- This indicates that there are no secondary alignments, meaning no reads aligned to multiple locations (which can happen in regions of the genome with repetitive sequences).

### 4. **2,368,833 + 0 supplementary**

- There are 2,368,833 supplementary alignments, which are partial or chimeric alignments, where a read is split across different regions. This often happens with structural variants or when parts of the read align to different locations.

### 5. **43,245,625 + 0 duplicates**

- 43,245,625 reads are marked as duplicates. Duplicates are usually caused by PCR amplification during library preparation and are flagged because they represent the same original DNA fragment.

### 6. **43,245,625 + 0 primary duplicates**

- All of the duplicates mentioned above are primary duplicates, meaning they correspond to the main alignment of the reads.

### 7. **303,548,330 + 0 mapped (98.92% : N/A)**

- 303,548,330 out of 306,850,015 reads are mapped to the reference genome, representing 98.92% of the total reads. This is a very high mapping rate, indicating good quality sequencing data.

### 8. **301,179,497 + 0 primary mapped (98.92% : N/A)**

- Out of the primary reads (non-duplicates, non-secondary), 301,179,497 out of 306,850,015 are mapped to the reference genome, which also represents 98.92% of the total primary reads.

### 9. **304,481,182 + 0 paired in sequencing**

- All 304,481,182 reads are paired-end reads, meaning that for each read, a mate read was sequenced from the opposite direction.

### 10. **152,240,591 + 0 read1**

- 152,240,591 reads correspond to the first read in each pair (read 1).

### 11. **152,240,591 + 0 read2**

- The same number of reads corresponds to the second read in each pair (read 2).

### 12. **279,510,516 + 0 properly paired (91.80% : N/A)**

- 279,510,516 read pairs (91.80% of the total) are properly paired. This means both mates in a pair are mapped correctly with respect to the expected insert size and orientation.

### 13. **300,420,946 + 0 with itself and mate mapped**

- 300,420,946 reads are paired where both the read and its mate are mapped to the reference.

### 14. **758,551 + 0 singletons (0.25% : N/A)**

- 758,551 reads are singletons, meaning one read of the pair was mapped, but its mate was not. This is a small fraction (0.25%) of the total reads, indicating good data quality.

### 15. **18,774,010 + 0 with mate mapped to a different chr**

- 18,774,010 reads are paired with their mates mapped to a different chromosome. This can indicate structural variations or mapping errors, depending on the context.

### 16. **8,359,967 + 0 with mate mapped to a different chr (mapQ>=5)**

- Of the reads mapped to different chromosomes, 8,359,967 have a mapping quality score of at least 5. This suggests a relatively confident mapping despite the inter-chromosomal location of the mates.
- This also suggests that we'd better do some filtering with mapQ, at least 5, perhaps 10 or 20.