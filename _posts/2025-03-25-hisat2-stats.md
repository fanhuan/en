---
layout: post
title: HISAT2 Alignment Statistics
categories: [notes]
tags: [bioinformatics]
---

I'm trying to tidy the mapping statistics given by HISAT2 into a table. First let's get on the same page with the statistics it spits out.

For example:

```
50964542 reads; of these:
  50964542 (100.00%) were paired; of these:
    2909022 (5.71%) aligned concordantly 0 times
    45584085 (89.44%) aligned concordantly exactly 1 time
    2471435 (4.85%) aligned concordantly >1 times
    ----
    2909022 pairs aligned concordantly 0 times; of these:
      205308 (7.06%) aligned discordantly 1 time
    ----
    2703714 pairs aligned 0 times concordantly or discordantly; of these:
      5407428 mates make up the pairs; of these:
        2967368 (54.88%) aligned 0 times   
        2131328 (39.41%) aligned exactly 1 time
        308732 (5.71%) aligned >1 times
97.09% overall alignment rate
```

Line by line:

```
50964542 reads; of these:                                                      # Total read pairs
  50964542 (100.00%) were paired; of these:                                    
    2909022 (5.71%) aligned concordantly 0 times                               # Unaligned Concordant Pairs
    45584085 (89.44%) aligned concordantly exactly 1 time                      # Concordant Unique Pairs
    2471435 (4.85%) aligned concordantly >1 times                              # Concordant Multi-mapped
    ----
    2909022 pairs aligned concordantly 0 times; of these:
      205308 (7.06%) aligned discordantly 1 time                               # Discordant Alignments
    ----
    2703714 pairs aligned 0 times concordantly or discordantly; of these:      # Unaligned Pairs Total
      5407428 mates make up the pairs; of these:
        2967368 (54.88%) aligned 0 times                                       # Single-end Unaligned
        2131328 (39.41%) aligned exactly 1 time                                # Single-end Unique
        308732 (5.71%) aligned >1 times                                        # Single-end Multi
97.09% overall alignment rate                                                  # Overall Alignment Rate
```

This stats tells us the underlying logic of HISAT2.

1. Check whether reads are paired. In this case all of them are (100%)

2. Try to align reads in pairs. This resulted in three groups:
- __Concordant Unique Pairs__ (45584085). This means both mates aligned uniquely in correct orientation/distance. These are the high quality alignment. 
- __Concordant Multi-mapped__ (2471435). Both mates aligned to multiple locations in correct orientation/distance. Likely to repetitive regions.
- The rest (2909022). For a lack of better words let's call this group __Non-Concordant Pairs__.

3. Now it is about different situations within the __Non-Concordant Pairs__.
- __Discordant alignment__ (205308): this means both mates aligned uniquely, but not concordantly (not in correct orientation or distance). These alignments are interesting since they could suggest potential structural variation or mis-assemblies.
- Single-end (2703714 pairs or 5407428 mates). For the rest of reads, basically HISAT2 failed to treat them as pairs, and now is trying to salvaged them individually as single-end. For these mates, there are obviously three situations: __Single-end Unaligned__ (2967368), __Single-end Unique__ (2131328) and __Single-end Multi__ (308732). Among those, the only thing that could be informative is the Single-end Unique.

4. Now you might have guessed how the __overall alignment rate__ is calcualted: 1- Single-end Unaligned/(Total read pairs * 2), since those are the only reads that faied to map to anywhere in the reference. This could be due to distances between the ref and your sample, or an incomplete ref in terms of short read mapping.

I wrote a short [python script](https://github.com/fanhuan/script/blob/master/parse_hisat2_log.py) that takes the stderr of HISAT2 and tidy it up in the terminology defined in this post. 

