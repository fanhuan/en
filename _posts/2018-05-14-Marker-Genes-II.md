---
layout: post
title:  So you have the genome, and you still want the marker genes? II 
categories: [notes]  
tags: [phylogeny]

---

Last time we talked about using blast to cut out marker genes from whole genomes. This works very well for famous marker genes such as 16s or ITS where a lot of sequences are available in public databases.

However if you are working on some lesser known genes, or if you are trying to design primers for amplicon sequencing, an *in silico* PCR approach might be more appropriate. 

A good tool to use is [cutadaptor](https://cutadapt.readthedocs.io/en/stable/). It has a very thorough documentation. Here I demonstrate its usage using 27F and 1492R for 16s gene as an example but it should work for any pair of primers.

	primer sequences:
	27F: AGAGTTTGATCMTGGCTCAG
    1492R: CGGTTACCTTGTTACGACTT

One thing that I haven't figure out about cutadapt is whether it is able to deal with the reverse complement of the primers automatically. For now I need to manually do it.

pattern1 = 'CAYCCNGCHCGYGAYATGC...GGYTTTGCYTTYGGBYTWGG'
   
pattern2 = 'CCWARVCCRAARGCAAARCC...GCATRTCRCGDGCNGGRTG'

The command I used was `$cutadapt -g pattern* --discard-untrimmed -o output.fa input_genome.fa`

Here g means compulsary, i.e. needs to have both adaptors.

This would return you a fasta file with the sequences in between primers. One thing I would suggest to do afterwards is to check the lengths of those sequences to see whether they are within the range of the length of the marker gene you are looking for. Here is a snippet of code if you uses python:

	
	def len_filter_fa(fa,lower,upper):
	    if lower > upper:
	        sys.exit()
	        print('lower bound is higher than upper bound')
	    with open(os.path.splitext(fa)[0] + '_' + str(lower) + '-' + str(upper) + os.path.splitext(fa)[1], 'w') as outfh:
	        with open(os.path.splitext(fa)[0] + '_wronglength' + os.path.splitext(fa)[1], 'w') as other:
	            for record in SeqIO.parse(fa, 'fasta'):
	                if lower < len(record.seq) < upper:
	                    SeqIO.write(record, outfh, 'fasta')
	                else:
	                    SeqIO.write(record, other, 'fasta')
 

Leave a comment if you have a better way of doing this. Thanks!
