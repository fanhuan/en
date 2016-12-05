---
layout: post
title:  How to add boostrap value to your tree
categories: [notes]
tags: [Phylogeny]
---

I usually use newick format for phylogentic trees. There are two ways you can indicate bootstrap values in this format according to [this post](http://www.megasoftware.net/mega4/WebHelp/glossary/rh_newick_format.htm).


>((raccoon:19.19959,bear:6.80041)50:0.84600,((sea_lion:11.99700, seal:12.00300)100:7.52973,((monkey:100.85930,cat:47.14069)80:20.59201, weasel:18.87953)75:2.09460)50:3.87382,dog:25.46154);
>
or

>((raccoon:19.19959,bear:6.80041):0.84600[50],((sea_lion:11.99700, seal:12.00300):7.52973[100],((monkey:100.85930,cat:47.14069):20.59201[80], weasel:18.87953):2.09460[75]):3.87382[50],dog:25.46154);


However, figtree only accepts the first one. My bootstrap values are usually generated using consense from the phylip package. They give you the consensus tree with bootstrap values for each branch as branch length. To incorporte this value back to your tree (supposingly the topologies are the same), I need to write a python script.

