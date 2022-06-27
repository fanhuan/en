---
layout: post
title:  So you have the metagenome, and you still want the marker genes?  
categories: [notes]  
tags: [phylogeny]

---

Several tools can be used to pull out reads originating from marker genes from metagenomic sequencing and assemble them. The logic of doing this is that we have a much better database of marker genes than whole genomes. I never had any luck with [EMIRGE](https://github.com/csmiller/EMIRGE) though I tried several time. Don't know what was the problem. The iterations never finishes. I haven't tried [REAGO](https://academic.oup.com/bioinformatics/article/31/12/i35/215357) either. Please leave a comment if you like it. Here I am only going to talk about [Matam](https://github.com/bonsai-team/matam#de-novo-assembly).

The documentation is not great yet and reading issues on its repo has been very helpful. Here is my command:

	matam_assembly.py -d database_path/SILVA_128_SSURef_NR95 -i test.fq --cpu n --max_memory 10000 -v --perform_taxonomic_assignment -o test --coverage_threshold 500 
	
Things to note here:

1. Only takes single end!
2. Cannot take gzipped file (maybe I will send a pull requet)
3. Need to specify `--perform_taxonomic_assignment` otherwise will only do assembly of 16s contigs. That classification is done by [rdp classifier](https://github.com/rdpstaff/classifier).
4. `--coverage_threshold 500` is very important for big dataset.

With this, for a dataset with about 10G gzipped data, it takes somewhere around 3-7 days to finish. So again setting the coverage threshold helps. 

__Some explanation on the output__

1. .final_assembly.fa This contains the assembly results of 16s contigs in fasta format. The id of each contig is an unique identifier, followed by the count. The count can be considered as average depth. See a more detailed explanation [here](https://github.com/bonsai-team/matam/issues/75). 
2. .krona.tab This is the txt format for the .krona.html. Using krona you can turn this txt format into html. The numbers in the first column of .krona.tab is the count of that contig. 
3. .rdp.tab This contains the same information as .krona.tab but in a different format. The first column is the unique identifier of that contig. The numbers after different level of classification is the probability of this contig being that classification.

__Some quality control__

The author is working on more robust way of filtering low count contigs but for now I am going to do it conservative while brutally, getting rid of any contig with count lower than 1. Snippet of code:

	def count_filter_fa(fa,lower):
	    import os
	    from Bio import SeqIO
	    outfile = os.path.splitext(fa)[0] + '_' + str(lower) + os.path.splitext(fa)[1]
	    in_rdp = fa.replace('final_assembly','rdp')
	    in_rdp = in_rdp.replace('.fa','.tab')
	    out_rdp = outfile.replace('final_assembly','rdp')
	    out_rdp = out_rdp.replace('.fa','.tab')
	    in_krona = in_rdp.replace('rdp','krona')
	    out_krona = out_rdp.replace('rdp','krona')
	    count_dic = {}
	    # filter final_assembly.fa
	    with open(outfile, 'w') as outfh:
	        for record in SeqIO.parse(fa, 'fasta'):
	            count = float(record.description.split('=')[-1])
	            if count > lower:
	                SeqIO.write(record, outfh, 'fasta')
	                count_dic[record.id]=''
	    # filter .krona.tab
	    with open(in_krona) as fh:
	        with open(out_krona,'w') as outfh:
	            for line in fh:
	                if float(line.split()[0]) > lower:
	                    outfh.write(line)
	                    
	    # filter .rdp.tab
	    with open(in_rdp) as fh:
	        with open(out_rdp,'w') as outfh:
	            for line in fh:
	                if line.split()[0] in count_dic:
	                    outfh.write(line)
	                    

Afterwords if you wish to generate the new krona html file you can run `ktImportText yourNew.krona.tab -o outfile.html`.

### Epilogue

One thing I wanted to do but failed to do is to calculate a rarefaction curve based on the 16s contigs assembled by matam and the classification done later by rdp. I was trying to use part of the [muthur pipeline](https://www.mothur.org/wiki/MiSeq_SOP#Alpha_diversity). However, this requires me to have a block of perfectly aligned sequences. It did not work for my data, since a lot of my matam assemblies are partial 16s, and there won't be much if any overlap for the whole dataset. 

However there are many other ways of calculating the rarefaction curves. If we insists on using 16s data, the `rarecurve` function from the `vegan` package should be sufficient. Microbial ecology is after all community ecology. See a [tutorial in R](https://colisci.univie.ac.at/uploads/media/Basic_analysis_in_R.pdf) for detailed code.

If the ultimate goal is estimate the depth of the sequencing, it can be done directly from metagenomic sequencing. Several tools are available such as [this one](https://msystems.asm.org/content/3/3/e00039-18).
