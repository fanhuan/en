---
layout: post
title:  Annotate my transcripts
categories: [notes]
tags: [Open lab note - Ficus RNA-seq]
---

I'm still using the old school BLAST to annotate my ficus RNA-seq data. I tried to annotate them based on the closest-related species with genome annotation available, which are things in Rosaceae (peach, apple and plume, according to [phytozome](http://phytozome.jgi.doe.gov)). However those genomes are annotated mainly by blasting against Arabidopsis. Therefore I decided to blast against arabidopsis directly. After the first round of tblastx, only half of my contigs have hits. For the other half, I blast them against a combined database of the three Rosaceae species. Only got a couple of thousands more hits. What are the rest of the things? Are they all novel to ficus? I want to do a blast against everything in NCBI but I do not want to download the database. Maybe I should. The online version is taking forever and ended up with an error page last night. Hope this new one works.

I failed again...

The error message goes like: 
>ERROR: Results for RID blabla not found 	

According to the explaination [here](http://www.ncbi.nlm.nih.gov/staff/tao/URLAPI/new/node99.html), it means that "RID has expired or is incorrect". Probably the job has been run too long (2h in my case) and exceed the CPU limit (it was 60min in 2007 according to [the same post](http://www.ncbi.nlm.nih.gov/staff/tao/URLAPI/new/node99.html)).

Alright, split it into halves. 


VUV1FV51015: 1sthalf.fa 00:47:22

VUVRHDX9014: 3rdquarter.fa failed

VUW6E20K01R: 7thokta.fa failed

VUWK34XN014: 15-16.fa failed

VUWVV687014: 31_32.fa failed

VUX3ZDXW014: 63_64.fa 00:11:52


The fact that the middle ones failed before the first one suggest it might not be a problem with CPU time limitation. What else can it be then?




