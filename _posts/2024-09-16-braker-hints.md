---
layout: post
title: Why doesn't braker make a prediction in the regions with hints?
categories: [genome annotation]
tags: [bioinformatics]
---



I have been using braker3 to annotate a novel genome. It is fast and so far has been pretty accurate except one problem. 5% of the time, it will fail to predict a gene with sufficient hints file evidence from RNA-seq and protein homology. Before moving to other tools, I'd like to see whether I have explored the full potential of braker.



The first option that came to my mind is `--alternatives_from_evidence` based on [this discussion](https://github.com/Gaius-Augustus/BRAKER/issues/40). However it turns out that this option is true by default. 
