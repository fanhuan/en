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


However, figtree only accepts the first one. A snippet of code to convert between the two:

	import re
	with open('input.tre') as fh:
    input_tree = ''.join(line.rstrip() for line in fh.readlines())                       
	bs_regex = re.compile(':\d+\.\d+\[\d+\]') 
	bss = bs_regex.findall(input_tree) 
	for item in bss:
	    bs = item.split('[')[1][:-len(']')]
	    bl = item.split('[')[0]
	    new_item = bs + bl 
	    new_tree = input_tree.replace(item, new_item,1)
	    input_tree = new_tree

	print(new_tree)

If you are generating bootstrap values using consense from the phylip package, consider this snippet of code from [Biopython](http://biopython.org/wiki/Phylo):

Branch Support

To get the branch support of a specific tree, we can use the get_support method.

>from Bio import Phylo  
>from Bio.Phylo.Consensus import *  
>trees = list(Phylo.parse('fileWithAllTheTrees', 'newick'))  
>target\_tree = Phylo.parse('outtreeGenerateByConsense','newick')  
>support\_tree = get_support(target\_tree, trees)

