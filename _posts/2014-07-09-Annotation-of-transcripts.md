---
layout: post
title:  Annotate my transcripts
categories: [notes]
tags: [Open lab note - Ficus RNA-seq]
---

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
	
######Another day goes by#############################
20140710

tblastx still running... It is much slower than blastx.




