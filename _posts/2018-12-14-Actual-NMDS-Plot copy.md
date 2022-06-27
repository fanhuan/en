---
layout: post
title:  NMDS plot with drivers
categories: [notes]
tags: [Stats]
---

I am still following this very help [post](https://jonlefcheck.net/2012/10/24/nmds-tutorial-in-r/) talking about NMDS from a community ecology point of view. 

## Step 1: NMDS

	N_matrix <- t(as.matrix(data))
	# Collaps into two dimension using NMDS argorithm
	N_NMDS=metaMDS(N_matrix, # Our distance matrix
	                     k=2) # The number of reduced dimensions
	# stress = 0.13 for Positive Ions. <0.1 is considered great.
	# stress = 0.06 for negative Ions. still pretty good
	stressplot(N_NMDS)
	# stress plot looks great too.
	# Now Plot 
	points <- as.data.frame(N_NMDS$points)
	colnames(points) <- c('NMDS1','NMDS2')

## Step 2: Hierarchical clustering based on NMDS result


multi-pairwise comparison Tukey’s tests to determine how the communities are different with respect to vegetation structure (tree, herbaceous community layer and C4 grass richness abundance and basal area/ cover) and environmental parameters

## Or go direct to [HCPC](http://www.sthda.com/english/articles/31-principal-component-methods-in-r-practical-guide/117-hcpc-hierarchical-clustering-on-principal-components-essentials/) (Hierarchical Clustering on Principal Components)
Then later just switch PC1 PC2 with NMDS1 or NMDS2.

OK, now the problem of clustering has been solved (clustering based on decomposed data instead of original ones), and it gives as some tests on how significantly different each variable is in different clusters. 

hai bao cu



