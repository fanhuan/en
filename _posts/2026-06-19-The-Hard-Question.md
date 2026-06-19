---
layout: post
title: The Hard Question
categories: [notes]
tags: [bioinformatics]
---

When I was doing my first year of postdoc, I was supposed to extend my OPT (optional practical training), when I learnt that my university is no longer listed on e-verify and cannot hire me under a OPT any more. My OPT expires in about 40 days. I need to find another employer that is on e-verify before that in order to stay in the US. A friend very nicely referred me to a start-up company and I've got to meet the team. I remember two things from those interviews. The first one is this very professional lady, who worked for a prominent company, telling me that she joined this start-up due to family considerations. The other thing I remember was one of the questions from the CEO:"How do you distinguish rare variants from sequencing error?"

I don't quite remember how I answered. In fact, to this day, I don't know the answer. 

Today let's make some attempts at least try to understand the problem that we are facing.

# Individual level vs. Population level

First of all, one need to understand that rare variants is a population-level concept, while sequencing error is at read level, and can be minimized at individual level.


# Quality score 
One obvious tool is quality score. Quality score of that base at fastq level, qulity score of the variant. If this variant got good coverage, 