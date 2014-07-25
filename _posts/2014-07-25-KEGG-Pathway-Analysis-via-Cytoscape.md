---
layout: post
title:  KEGG Pathway Analysis via Cytoscape
categories: [notes]
tags: [Open lab note - KEGG Pathway]
---

Since no KEGG pathway was enriched from my differentially expressed genes, I decided to apply a top-down stratigy: look at the gene expression of the pathway that I wish was enriched.

The first one I looked at is Auxin in the Plant Hormone Signal Transduction [KEGG map](http://www.genome.jp/kegg-bin/show_pathway?ath04075).

1. Prepare expression data for genes involved in this pathway.

	On the [page](http://www.genome.jp/kegg-bin/show_pathway?ath04075) of the map, choose the species I annotated with from the drop down menu and go. Good to see that all the boxes(objects) are green, which means it is a complete pathway in this organism. Click on 'Download KGML' to save the map in xml format. Click on one object, for example ARF, you'll see the genes that produce this object. Luckily all the gene names are in the url, making it easy to grab the counts/expression data of those genes from my count data. I keep the expression data for different objects in seperate files.

2. Install CyKEGGParser from Cytoscape

	Just follow the instructions in the [manual](http://molbiol.sci.am/big/apps/cy_kp/jar/CyKEGGParser_User_Manual.pdf).

3. Load the xml format kegg map
