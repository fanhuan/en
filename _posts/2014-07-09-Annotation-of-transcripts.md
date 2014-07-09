---
layout: post
title:  Annotate my transcripts
categories: [notes]
tags: [Open lab note - Ficus RNA-seq]
---

I'm still using the old school BLAST to annotate my ficus RNA-seq data. I tried to annotate them based on the closest-related species with genome annotation available, which are things in Rosaceae (peach, apple and plume, according to [phytozome](http://phytozome.jgi.doe.gov)). However those genomes are annotated mainly by blasting against Arabidopsis. Therefore I decided to blast against arabidopsis directly. After the first round of tblastx, only half of my contigs have hits. For the other half, I blast them against a combined database of the three Rosaceae species. Only got a couple of thousands more hits. What are the rest of the things? Are they all novel to ficus? I want to do a blast against everything in NCBI but I do not want to download the database. Maybe I should. The online version is taking forever and ended up with an error page last night. Hope this new one works.