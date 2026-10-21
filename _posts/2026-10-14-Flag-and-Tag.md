---
layout: post
title: Flag and Tag 
categories: [notes]
tags: [bioinformatics]
---

Working with SAM again. I feel I've never really understand all the fields. So today we will give two things another try, Flag and Tag.

Firstly, flag.

Here is a random line from one of my SAM:

```
INSTR:1:FLOWCELL:2:2390:11059:25163     99      chr9    1521    0       150M    =       1822    447     CCATCT…CTCGG    IIII…9IIIIIIII-IIIIIIII XA:Z:chr9,+11755,150M,1;        MC:Z:146M       MD:Z:32T52C64   PG:Z:MarkDuplicates     RG:Z:SAMPLE_A_LIB1_FLOWCELL_L2  NM:i:2  AS:i:140        XS:i:145
```
Firstly there are 11 fixed columns that every sam should have

1. read name: INSTR:1:FLOWCELL:2:2390:11059:25163
2. FLAG: 99
3. chromosome: chr9
4. start position: 1521
5.  