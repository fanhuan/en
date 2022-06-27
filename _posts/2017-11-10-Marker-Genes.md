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

	$makeblastdb -in genomes.fa -input_type fasta -title Esc_LSU -parse_seqids -out Esc_LSU -dbtype nucl
	$blastn -db Esc_LSU -out Esc_LSU.m6 -query Cameron_LSU_2003.fa -max_hsps 1 -outfmt 6
	
qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore
ICBGSID742	NODE_1_length_598385_cov_54.978813	100.000	598385	0	0	1598385	1	598385	0.0	1.105e+063

3. Extract gene sequences based on blast results.

		import pandas as pd
		from collections import Counter
		from Bio import SeqIO
		import os
		fasta_path = '/home/hfan/projects/Escovopsis/fasta/'
		df = pd.read_table('/home/hfan/projects/Escovopsis/LSU/Esc_LSU.m6', 
		        names = ['qseqid','sseqid','pident','length','mismatch','gapopen','qstart','qend', 
		                 'sstart','send','evalue','bitscore'])
		df['SID'],df['contig'] = df.sseqid.str.split('_',1).str
		for SID in df.SID.unique():
		    sub_df = df.loc[df['SID'] == SID]
		    contig = Counter(sub_df['contig']).most_common(1)[0][0]
		    sstart = int(Counter(sub_df.loc[sub_df['contig'] == contig]['sstart']).most_common(1)[0][0])
		    send = int(Counter(sub_df.loc[sub_df['contig'] == contig]['send']).most_common(1)[0][0])
		    print(SID,contig,sstart,send)
		    for file in os.listdir(fasta_path):
		        if file.split('_')[2][:-len('.fa')] == SID[len('ICBGSID'):]:
		            with open(file[:-len('.fa')] + '_LSU.fa','w') as fh:
		                for record in SeqIO.parse(fasta_path + file, 'fasta'):
		                    if record.id[len(SID)+1:] == contig:
		                        if sstart > send:
		                            sstart,send = send, sstart
		                        fh.write('>{} {}-{}\n'.format(record.id,sstart,send))
		                        seq = str(record.seq[sstart-1:send])
		                        print(seq)
		                        fh.write(seq + '\n')

4. Add those sequences to the existing marker gene sequences. Now I need to make a gene tree! When was the last time that I've done that? When I was writing my Chinese thesis.

a. alignment: mafft
		
	$mafft --auto --maxiterate 1000 --thread 40 LSU.fa > LSU.afa
	
If you are combining LSU sequences from different batches, sometimes the primers used are different. This would result in large chuncks of gaps in the beginning and the end of the alignment file. The following snippet of code deals with three things:

- Trim gaps in the beginning and end of mafft alignment output
- convert fasta alignemnt to phylip 
- Deal with the 10-charater species name requirement of phylip

		from Bio import AlignIO
		import sys
		afa = "LSU.afa"
		phylip = afa.split('.')[0] + '.phylip'
		namefile = afa.split('.')[0] + '.namedic'
		align = AlignIO.read(afa, "fasta")
		namedic = {}
		start = []
		end = []
		# Trim the alignment to have everything aligned.
		for record in align:
		    bp_regex = re.compile("[atcgATCG]")
		    matches = bp_regex.finditer(str(record.seq))
		    positions = [m.start() for m in matches]
		    start.append(positions[0])
		    end.append(positions[-1])
		
		sstart = max(start)
		send = min(end)
		
		for record in align:
		    lsl = len(record.id)
		    if lsl >= 10:
		        ssl = record.id[:10]
		    else:
		        ssl = record.id
		    appendix = 0
		    while ssl in namedic:
		        ssl = ssl[:-len(str(appendix))] + str(appendix)
		        appendix += 1
		    namedic[ssl] = record.id
		    record.id = ssl
		    record.seq = record.seq[sstart:send+1]
		    
		AlignIO.write(align,phylip,'phylip')
		with open(namefile,'w') as fh:
		    for key in namedic:
		        fh.write('{}: {}\n'.format(key,namedic[key]))

b. RAxML for tree  
We have RAxML installed on the server but for some reason it is very slow. I use the online version provided by [Science Gateway](https://www.phylo.org/portal2/login!input.action). You want the RAxML_bipartitionsBranchLabels.result file. It contains the bootstrap value. Refer to this post for converting it to another way of representing bootstrap values that is Figtree friendly.

The end.
	 


