---
layout: post
title:  So you have the genome, and you still want the marker genes?  
categories: [notes]  
tags: [phylogeny]

---

This happens when you have some genomes, and cannot say no to the gene sequences from other samples, since you might not be sequencing them for now or forever. 

Here is a demonstration for how to fish out LSU (large sub-unit) genes out of Escovopsis (my new client) genome. 

Input: LSU from another strain of Escovopsis. This could be downloaded from NCBI. I asked for one from an undergrad who is doing LSU sequencing of them.

1. Downloading Escovopsis LSU sequences from Cameron's 2003 paper.

		from Bio import Entrez
		import re,sys
		Entrez.email="myemail@address"
		
		def pop_acc(start,end):
		    '''
		    To populate the accession list:
		    Takes the start and end of a series of accession number,
		    return the full list
		    
		    '''
		    start_d = re.search(r'\d+', start).group()
		    start_a = start[:-len(start_d)]
		    end_d = re.search(r'\d+', end).group()
		    end_a = end[:-len(end_d)]
		    if start_a == end_a:
		        if int(start_d) < int(end_d):
		            acc_list = [start_a + str(x) for x in range(int(start_d), int(end_d) + 1)]
		        else:
		            print('{} usually happens before {}, have you switched the order of the two?'.format(start, end))
		            sys.exit()
		    else:
		        print('{} and {} are not from the same series'.format(start,end))
		        sys.exit()
		    return(acc_list)    
		    
		acc_list = pop_acc('AY172599','AY172615') + \
		            ['AF339530','U00748', 'U00756', 'U17396', 'U17416', 'U48428', 'U57681'] + \
		            pop_acc('AF543786','AF543793')
		
		with open('Cameron_LSU_2003.fa','w') as fh:
		    for acc in acc_list:
		        handle = Entrez.efetch(db="Nucleotide",id=acc,retmode="xml")
		        acc_info = Entrez.read(handle)
		        fh.write('>' + acc_info[0]['GBSeq_source'] + '\n')
		        fh.write(acc_info[0]['GBSeq_sequence'] + '\n')

2. Make a database using the genomes that we are going to pull LSU genes from. Yes, old school blast still works very well!

Since they are all escovopsis, I choose to use megablast	of blastn: "Traditional megablast used to find very similar (e.g., intraspecies or closely related species) sequences".

	$makeblastdb -in temp.fa -input_type fasta -title Esc_LSU -parse_seqids -out Esc_LSU -dbtype nucl
	$blastn -db Esc_LSU -out Esc_LSU.m6 -query Cameron_LSU_2003.fa -max_hsps 1 -outfmt 6
	
qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore
ICBGSID742	NODE_1_length_598385_cov_54.978813	100.000	598385	0	0	1598385	1	598385	0.0	1.105e+06

