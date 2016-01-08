---
layout: post
title:  Blast ouput in xml format
categories: [notes]
tags: [Open lab note - Ficus RNA-seq]
---


Ierror: failed to push some refs to 'https://github.com/fanhuan/script'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
Huans-MacBook-Pro:script Huan$ git pull
remote: Counting objects: 16, done.
remote: Total 16 (delta 2), reused 2 (delta 2), pack-reused 14
Unpacking objects: 100% (16/16), done.
From https://github.com/fanhuan/script
   20639e2..1813ce5  master     -> origin/master
Auto-merging simRadAlignment.py
CONFLICT (add/add): Merge conflict in simRadAlignment.py
Auto-merging shared_diversity.py
CONFLICT (add/add): Merge conflict in shared_diversity.py
Removing prune_newick
Auto-merging filter_kmer_from_pattern.py
CONFLICT (content): Merge conflict in filter_kmer_from_pattern.py
Removing 1simulation.R
Automatic merge failed; fix conflicts and then commit the result.

I'm still using the old school BLAST to annotate my ficus RNA-seq data. I tried to annotate them based on the closest-related species with genome annotation available, which are things in Rosaceae (peach, apple and plume, according to [phytozome](http://phytozome.jgi.doe.gov)). However those genomes are annotated mainly by blasting against Arabidopsis. Therefore I decided to blast against arabidopsis directly. After the first round of tblastx, only half of my contigs have hits. For the other half, I blast them against a combined database of the three Rosaceae species. Only got a couple of thousands more hits. What are the rest of the things? Are they all novel to ficus? I want to do a blast against everything in NCBI but I do not want to download the database. Maybe I should. The online version is taking forever and ended up with an error page last night. Hope this new one works.

I failed again...

The error message goes like: 
>ERROR: Results for RID blabla not found 	

According to the explaination [here](http://www.ncbi.nlm.nih.gov/staff/tao/URLAPI/new/node99.html), it means that "RID has expired or is incorrect". Probably the job has been run too long (2h in my case) and exceed the CPU limit (it was 60min in 2007 according to [the same post](http://www.ncbi.nlm.nih.gov/staff/tao/URLAPI/new/node99.html)).

Alright, split it into halves. 


VUV1FV51015: 1sthalf.fa 00:47:22

VUVRHDX9014: 3rdquarter.fa failed

VUW6E20K01R: 7thokta.fa failed

VUWK34XN014: 15-16.fa failed

VUWVV687014: 31_32.fa failed

VUX3ZDXW014: 63_64.fa 00:11:52


The fact that the middle ones failed before the first one suggest it might not be a problem with CPU time limitation. After a while the last one failed as well, followed by the first one. Cannot find other info on this error. Alright! Download the whole database!

By reading the [BLAST database documentation](ftp://ftp.ncbi.nlm.nih.gov/blast/documents/blastdb.html), it seems like that I should be able to download the database by using a perl script called update_blastdb.pl. But you need to install perl-doc. 

Helpful options of update_blastdb.pl

	--showall Show all available pre-formatted BLAST databases (default: false). The output of this option
         lists the database names which should be used when requesting downloads or updates using this
         script. 
   However there is no option to specify which database to download... Also this option failed with error message:
         
    $ update_blastdb.pl --showall
	  Connected to NCBI
	  Use of uninitialized value $retval[0] in concatenation (.) or string at /home/heather/bin/update_blastdb.pl line 134.
	
Other opitons:
		         
	--passive
         Use passive FTP, useful when behind a firewall (default: false).
         
	--decompress
         Downloads, decompresses the archives in the current working directory, and deletes the
         downloaded archive to save disk space, while preserving the archive checksum files (default:
         false).  Note: Using this option may require more computing resources than using your system's
         native decompression tool(s).

Did not work. Download the database directly from ftp. Target:
refseq_rna.*tar.gz     | NCBI Transcript reference sequences

	$ wget ftp://ftp.ncbi.nih.gov/blast/db/refseq_rna.03.tar.gz
	
untar, blast:

	$ tblastx -db refseq_rna -query nohit_FFtRosa.0708.tblastx.xml.fa -num_threads 12 -outfmt 5 -evalue 1e-10 -out FFtref.0708.tblastx.xml &
	[1] 39530
	$ BLAST Database error: Could not find volume or alias file (refseq_rna.00) referenced in alias file (/home/heather/Projects/Ficus/Transcriptome/blast/refseq_rna).
	
I should download 00, 01 and 02 as well? I thought it was only numbering and 03 is newest version! 
Yes. download refseq_rna.*.tar.gz... Maybe there is a limitation on size of one database. Lucking the refseq_rna has only 4 parts. Some of the database has >40!

	$ tblastx -db refseq_rna -query nohit_FFtRosa.0708.tblastx.xml.fa -num_threads 12 -outfmt 5 -evalue 1e-10 -out FFtref.0708.tblastx.xml &
	
######Another day goes by

20140710

tblastx still running... It is much slower than blastx.
It's been a day and I just realize that it's only finished 20% of the transcripts. And I just noticed that we have 32 processors on the server. I decide to kill the job and start again using 30 cores. 

	$ nohup tblastx -db refseq_rna -query nohit_FFtRosa.0708.tblastx.xml.fa -num_threads 30 -outfmt 5 -evalue 1e-10 -out FFtref.0710.tblastx.xml &
	[1] 46225
	
I killed it because I found that I do not need to start from the start. I just need to finish blasting the rest of the transcripts.

	$ tail -112218 nohit_FFtRosa.0708.tblastx.xml.fa > nohit_FFtRosa.0710.tblastx.xml.fa 
	$ head nohit_FFtRosa.0710.tblastx.xml.fa 
	>comp108745_c0_seq3 len=239 path=[1:0-168 1894:169-238]
	GGAAAGAGCGAAAGTCTGGGATTTTCATGGCGGAGACTGGAACGAAGAGGAAGCGAATGG
	GGGAGAAAGGAGGAGCAATTTTCCCGAAAACGAAATCAAATTGGCCAGCGATCAAACCAA
	AACAAAATCTCCACATCACTCGTCTCAAAGATACCGATCTTTTCACGGTGTGAATCTGAA
	ATACCCCCCCATATTTCTCTCCTCTGTAATTGGCTTGCTTTTTTTTTTTTAATAAATAT
	$ nohup tblastx -db refseq_rna -query nohit_FFtRosa.0710.tblastx.xml.fa -num_threads 30 -outfmt 5 -evalue 1e-10 -out FFtref.0710.tblastx.xml &
	[1] 46737

OK. This is still going to take 1.5 days. Prepare for the following analysis.
Depending on want information the GO analysis needs, we need to parse the blasting to refRNA different. Now only the description is kept. We want the gene ID apparently. But do we want the description as well? How do we have a geneID2GO map? 
gi|112984375|ref|NM_001043694.1| Bombyx mori ribosomal protein L14 (RpL14), mRNA
Which one to keep and how to tranlate it into GO map?

We also need the length Data.

	
######Another day goes by

20140711
1. Which ID to keep, the ginumber one or the species initial number one? How to link them to GO terms?
	format of the blastRecord.alignments[0].title
				
	gi|426369154|ref|XR_175802.1| PREDICTED: Gorilla gorilla gorilla uncharacterized LOC101147172 (LOC101147172), misc_RNA

Rstudio:http://210.72.93.47:8787/

All need to specify a certain species. Got interested in [the XSLT solution](https://www.biostars.org/p/1226/).

>Sidenote:
XSLT(Extensible Stylesheet Language Transformations)  
[A tutorial](http://www.w3schools.com/xsl/)
CSS = Style Sheets for HTML

$ git clone git://git.gnome.org/libxslt

$ xsltproc --novalid stylesheet.xsl "http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nucleotide&id=NM_030621&rettype=xml"
xsltproc: relocation error: xsltproc: symbol xsltMaxVars, version LIBXML2_1.0.24 not defined in file libxslt.so.1 with link time reference

Cannot fix this error. 

######Another week goes by

20140715

The server is down again. Got some advice from advisor. Need to balance between false positives and false negatives. I was using tblastx in order to annotate as many transcripts as possible while hoping to verify the hits by reciprocal best hits (RBH) later. However [reciprocal BLAST does not fully take into account situtations where the gene history is complicated with gene duplications](http://www.flyrnai.org/RNAi_orthology.html). While everybody is using blastx. Trying to figure out what it the best way to annatate. Ran into [this paper](http://www.plosone.org/article/info%3Adoi%2F10.1371%2Fjournal.pone.0101850). Seems like I have to stick to blast since my reference is so far away.

Got some inspiration from [this](http://ged.msu.edu/angus/tutorials/reciprocal-blast.html), probably written by Titus. Modify the parseXML.py to parse2XML.py

Emm. Maybe RBH is too strict? I decide to recess to writing for today. Maybe I should do half day writing and half day analysis.

######Another day goes by

20140716

Decided that it is better to write down the analysis that has been down to decide what other analysis are needed.
R asked me to install blast2go. It's a GUI. Could not load sequence files.
######Another day goes by
20140717

Keep writing the methods part for the differential analysis.
