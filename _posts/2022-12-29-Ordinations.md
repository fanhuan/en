---
layout: post
title: Ordination: nMDS, PCoA, CA
categories: [notes]
tags: [stats]
---

For some reason nMDS always separates the samples nicer than other ordination methods. Therefore I use it a lot. However it is hard to see the variance explained by each nMDS vectors. Therefore one should always consider a principle component method first (such as PCA, PCoA or CA).

My reference is mainly from this book: [Numerical Ecology with R-2nd edition](https://link.springer.com/book/10.1007/978-3-319-71404-2).

Today we will talk about CA(correspondence analysis). Some key facts:

1. It handles: presence-absence or abundance data (frequencies or frequencies like, dimensionally homogeneous, non-negative)
2. It is not influenced by double zeros. 
3. Asymmetrical
4. No pre-transformation is needed.
5. The variation explained by each orthogonal axes is measured by total inertia (sum of squares of all values in matrix Q bar) 
6. Two scalings: 1. rows(sites) are at the centroids of the columns (species). Default. 
