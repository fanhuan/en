---
layout: post
title:  NMDS plot from distance matrix
categories: [notes]
tags: [AAF]
---

A major part of my UW thesis is on reconstructing phylogeny from raw reads, without either assembly or alignment. I call this method assembly and alignment free (AAF) and one of my [github repo](https://github.com/fanhuan/AAF) is dedicated to it.

This year I started using this method on metagenomic data for clustering of different samples. Since the relationship between those samples are probably not tree-like, a NMDS plot might make more sense (Dr. Camila Carlos initially suggested this. Thanks Camila!).

Here is a very help [post](https://jonlefcheck.net/2012/10/24/nmds-tutorial-in-r/) talking about NMDS from a community ecology point of view. The only thing is that it started with raw species composition data instead of distance matrix, which is what I have. After some searches, I found that it is actually easier (as it should be) than starting from raw data. Here is some code to help you if you want to plot any of the distance matrix generated from the AAF package.

	library(MASS)
	library(ggplot2)
	library(ggrepel)
	# Read in the distance file, aaf.dist for example
	distance <- read.table(file = 'aaf.dist',skip = 1, row.names = 1)
	# Turn it into a matrix
	colnames(distance) <- rownames(distance)
	dis_matrix <- as.matrix(distance)
	# Collaps into two dimension using NMDS argorithm
	dis_nmds <- nmds(dis_matrix)
	# Plot 
	points <- as.data.frame(dis_nmds$points)
	colnames(points) <- c('NMDS1','NMDS2')
	ggplot(points) +
  		geom_point(aes(NMDS1,NMDS2),color = 'red') + 
  		geom_text_repel(aes(NMDS1,NMDS2,label = rownames(points)))
  		
---
# Update on MASS
It seems like the nmds function disappeared from the MASS package. Here is an alternative using vegan

	library(vegan)
	# Read in the distance file
	distance <- read.table(file = 'aaf.dist',
                       skip = 1, row.names = 1)
	# Turn it into a matrix
	colnames(distance) <- rownames(distance)
	dis_matrix <- as.matrix(distance)
	# Collaps into two dimension using the metaMDS function from vegan
	example_NMDS=metaMDS(dis_matrix, # Our distance matrix
                        k=2) # The number of reduced dimensions
	#Stress plot. See explaintation at https://jonlefcheck.net/2012/10/24/nmds-tutorial-in-r/
	stressplot(example_NMDS)
	plot(example_NMDS)
	ordiplot(example_NMDS,type="n")
	orditorp(example_NMDS,display="sites",col="red",air=0.01)
  
  		
   