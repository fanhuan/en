Why metagenomics (i.e. why not amplicon seequencing)?

1. Better resolution with species(not genus) diversity
2. Function CAPACITY prediction (yes, only capacity)
3. Composite genomes! God bless they are not the ones you were able to culture anyways!

This is how I work with composite genomes. Let me know how you do it differently and why. 
## Download raw reads from JGI

python3 ~/scripts/jgi-query.py http://genome.jgi.doe.gov/AttbisABBM2_FD/AttbisABBM_FD.info.html

## Maxbin

	for name in ABBM2 ACBM1 ACBM2 ACBM3 ALBM1 ALBM3 ASBM1 ASBM2 ASBM3; do ~/build/MaxBin-2.2.4/run_MaxBin.pl -contig /media/backup_2tb/Data/fungus_garden_bacteria/BAscaff/${name}_BAscaff/${name}_BAscaff.fa -reads $name.fq.gz -out ${name}_maxbin -thread 30; done
	
## checkM

	
	checkm lineage_wf folder_with_binned_fasta output_folder -x fasta 
	e.g. $checkm lineage_wf ABBM1_bin ABBM1_checkM -x fasta
Recommanded criterio from their paper:  
Completeness: >90%  
Contamination: <10%

## What are the things that passed the checkM check?

Look at the tree that is generated for each sample (all bins from that sample included), and identify their sister branch. Calculate ANI(average nucleotide identities) if there are multiple sister branches.

### How to calculate ANI

http://enve-omics.ce.gatech.edu/ani/
blastn based, cited 1020 times and 202 times this year. Should be fine.
This tool takes the ncbi nucleotide accession of genomes. To look for the accession number from IMG number (the ones that checkM uses), use the biosample number on the IMG page, for example, SAMN00839623 for [IMG 25130200](https://img.jgi.doe.gov/cgi-bin/mer/main.cgi?section=TaxonDetail&page=taxonDetail&taxon_oid=2513020051). Go to the corresponding [biosample page](https://www.ncbi.nlm.nih.gov/biosample/SAMN00839623) in NCBI, and click on the [Nucleotide](https://www.ncbi.nlm.nih.gov/nuccore?LinkName=biosample_nuccore&from_uid=839623) link at the bottom. In this case, there are >300 contigs and you'd better download the [assembly](https://www.ncbi.nlm.nih.gov/assembly?LinkName=biosample_assembly&from_uid=839623) instead.   

### Annotate the composite genomes
After calculate the ANI between the composite genomes and their sister branch in the checkM tree, most of them don't have a similarity higher than 97%. Therefore We need to annotate the composite genomes, hopefully get the 16s of them, and try to id them properly. Here we choose [prokka](https://github.com/tseemann/prokka) for annotation of prokaryotes.

	for name in /media/backup_4tb/Lily_backup/Pantoea/*.*
	do 
		base=${name%.fa*}
		for name in *.*; do base=${name%.fa*}; prokka $name --outdir $base --prefix $base --locustag $base; done

Get 16S sequences: Lily\_composite\_genomes.ipynb

If *\_16S\_nopartial.fa is not empty, then use it only. If empty, consult *_16S.fa

Blastn:

