---
layout: post
title: How to build a kraken2 database from scratch
categories: [notes]
tags: [lab note, liana]
---

# Download taxonomy

	$ kraken2-build --download-taxonomy --db kraken2_db
	
This downloading job is not trivial (35G), and the taxonomy folder can be shared across different kraken2 database, therefore I usually put it outside the `kraken2_db` folder, mark it by the date it was downloaded, and make a soft link inside the `kraken2_db` folder. I usually update this folder every half a year.

	kraken2_db$ mv taxonomy ../taxonomy20220725
	kraken2_db$ ln -s ../taxonomy20220725 taxonomy
	
# Download exsiting databases

	$ kraken2-build --download-library archaea --db kraken2_db

	rsync_from_ncbi.pl: unexpected FTP path (new server?) for https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/*"

This is a known [issue](https://github.com/DerrickWood/kraken2/issues/518). Fixed by changing ftp to https on line 46 of `rsync_from_ncbi.pl`.

	
	$ kraken2-build --download-library viral --db kraken2_db

	$ kraken2-build --download-library fungi --db kraken2_db

	$ kraken2-build --download-library bacteria --db kraken2_db

	Step 1/2: Performing rsync file transfer of requested files
	rsync: read error: Connection timed out (110)
	rsync error: error in socket IO (code 10) at io.c(794) [receiver=3.1.2]
	rsync: connection unexpectedly closed (5538447 bytes received so far) [generator]
	rsync error: error in rsync protocol data stream (code 12) at io.c(235) [generator=3.1.2]
	rsync_from_ncbi.pl: rsync error, exiting: 3072

The files for bacteria are >100G and network error might occur. Just re-run this command.

	Step 1/2: Performing rsync file transfer of requested files
	Rsync file transfer complete.
	Step 2/2: Assigning taxonomic IDs to sequences
	Processed 32364 projects (76359 sequences, 134.83 Gbp)... done.
	All files processed, cleaning up extra sequence files... done, library complete.
	Masking low-complexity regions of downloaded library...sed: couldn't write 60 items to stdout: No space left on device
	
The masking step will generate a tmp file that's about the same size as the `library.fna` file, therefore at least 135*2G of disk space is required to run this command.
	
In order to not re-download `library.fna`, we need to pick up from where this command failed: 'Masking low-complexity regions of downloaded library'. 

	for file in path_to_kraken2/kraken2/*
	do
		echo $file
		grep Masking $file 
	done

It appered in `add_to_library.sh` and `download_genomic_library.sh`. Apparently this is about the latter. Checking out the code in the bash file, it's calling  `mask_low_complexity.sh`

	kranken2_db$ ~/build/kraken2/mask_low_complexity.sh .
	/home/hfan/build/kraken2/mask_low_complexity.sh: line 17: KRAKEN2_PROTEIN_DB: unbound variable
	
Missing environment variable `KRAKEN2_PROTEIN_DB`. Here we are not building protein databases. 
	
	$ export KRAKEN2_PROTEIN_DB=‘’
	kranken2_db$ mask_low_complexity.sh .
	
It turns out that nothing was masked. `library.fna.masked` is empty. This is true for archaea, bacteria, fungi and virus.

# Add customized files

There's not much regulation on the customized files to be added except that 
1. it needs to be in fasta format
2. the record.id needs to be in this format: `>seq_id|kraken:taxid|1234567` here 1234567 needs to be the taxID of the sequence you are adding. If the taxID of the sequences is unknown, you can use a taxID that shares the most recent ancestor (for example, another strain under the same species or another species in the same genus) that is not present in your kraken2 database to represent it. You need to take record of couse. 

	for file in dir/*.fna
	do
		kraken2-build --add-to-library $file --db kraken2_db
	done

# Build!
	$kraken2-build --build --threads n --db kraken2_db
	
	Creating sequence ID to taxonomy ID map (step 1)...
	Sequence ID to taxonomy ID map complete. [4.092s]
	Estimating required capacity (step 2)...
	Estimated hash table requirement: 61765191680 bytes
	Capacity estimation complete. [30m6.427s]
	Building database files (step 3)...
	Taxonomy parsed and converted.
	CHT created with 16 bits reserved for taxid.
	Completed processing of 93901 sequences, 138676758167 bp
	Writing data to disk...  complete.
	Database files completed. [7h48m55.558s]
	Database construction complete. [Total: 8h19m6.133s]

# OK testing.


