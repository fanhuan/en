---
layout: post
title: Install and Running Picrust2 - Basic
categories: [notes]
tags: [python]
---

# Installation

I tried to install picrust2 following the [instructions](https://github.com/picrust/picrust2/wiki/Installation) they provided. Both the `Install from bioconda` and `Install from source` gave the same error:

	No module named 'conda._vendor.auxlib'
	
	
This is an error due to old version of [mamba](https://github.com/mamba-org/mamba/issues/1583), a solution was also provided there.

	$ conda create -y -n newenv "python=3.8" "mamba>=0.22.1"
	$ conda activate newenv
	
Now let's try install from bioconda again. 

	(newenv)$ mamba create -n picrust2 -c bioconda -c conda-forge picrust2=2.5.0

I ran into downloading problem, which as solved by scientific internet surfing (ke xue shang wang).

Now let's activate this environment

	(newenv)$ mamba activate picrust2
	Run 'mamba init' to be able to run mamba activate/deactivate 
	and start a new shell session. Or use conda to activate/deactivate.
	(newenv)$ mamba init
	
Close current terminal and start a new one.

	(base)$ mamba activate picrust2
	(picrust2)$ picrust2_pipeline -h
	
# Running

## Input

- fasta of amplicon sequences variants (study_seqs.fna)
- biom table of the abundance of each ASV across each sample (study_seqs.biom). I don't have this. A tab-delimited table with ASV ids as the first column and sample abundances as all subsequent columns will also work. I will use the latter.


## Options

--stratified: Flag to indicate that stratified tables should be generated at all steps, this can increase run-time but not by a lot.
 
 
## Run

	(picrust2)$ picrust2_pipeline.py -s test_rep.fna -i test_otu_table_total.txt -o test_picrust2 -p 12
	Warning - column named "taxonomy" in abundance table - if this corresponds to taxonomic labels this should be removed before running this pipeline.

OK removed. 

	$ cut -f 1-27 test_otu_table_total.txt > test.otu_table_total.txt
	$ picrust2_pipeline.py -s test_rep.fna -i test.otu_table_total.txt -o test_picrust2 -p 12
	Error running this command:
	place_seqs.py --study_fasta 26arialroot_16Sv3v4_rep.fna --ref_dir /home/hfan/build/anaconda3/envs/picrust2/envs/picrust2/lib/python3.8/site-packages/picrust2/default_files/prokaryotic/pro_ref --out_tree 26arial_16s_picrust2/out.tre --processes 12 --intermediate 26arial_16s_picrust2/intermediate/place_seqs --min_align 0.8 --chunk_size 5000 --placement_tool epa-ng

	Standard error of the above failed command:
	Stopping - all N input sequences aligned poorly to reference sequences (--min_align option specified a minimum proportion of 0.8 aligning to reference sequences).
	
So it failed at the first step, place_seqs.py, and the reason is that my 16 sequence cannot be found in the database. That is odd. Most of they can be classified using the silva database. That's going on?

Try another dataset. Noticed that the aligner they used is hmmalign.
Same error. Is it because my seqs are v3-v4 region therefore not long enough?

## Check the ref fasta

	(base)$ python ~/GitHub/script/seq_stats.py -i /home/user/build/anaconda3/envs/picrust2/envs/picrust2/lib/python3.8/site-packages/picrust2/default_files/prokaryotic/pro_ref/pro_ref.fna -f fasta
	Sample	NumSeq	Total_bp	Mean	Var	Min	Max	N50	N90
	pro_ref.fna 20000 31640000 1582.0 0.0 1582 1582 1582 1582
	
So all seqs are aligned and the lengths are 1582. My sequences are 400bp. Let's try `--min_align 0.25` (poorly) and maybe then `--min_align 0.2`(only one ASV aligned out of more than 2k) `--min_align 0.15` (5 aligned, then `2 of 5 ASVs were above the max NSTI cut-off of 2.0 and were removed from the downstream analyses.` so only 3 left!) `--min_align 0.1` (30 aligned, 10 of 30 ASVs were above the max NSTI cut-off of 2.0 and were removed from the downstream analyses, only 20 left). `--min_align 0.05` (34 of 63 ASVs were above the max NSTI cut-off of 2.0 and were removed from the downstream analyses.) `--min_align 0.01`(42 of 80 ASVs were above the max NSTI cut-off of 2.0 and were removed from the downstream analyses.)

OK this is not about the length.

## Reverse complement

Note that your input study sequences need to be on the positive strand! OK let's add every ASV's reverse compliment. 

	$ python ~/GitHub/script/complement.py -i test_rep.fna > test_rep_rc.fna

0.25 19 of 2386 ASVs were above the max NSTI cut-off of 2.0 and were removed from the downstream analyses.

OK. How about higher percentage?

0.8(default) 19 of 2386 ASVs were above the max NSTI cut-off of 2.0 and were removed from the downstream analyses. 

The same! OK so --min_align is calcuated based on the query, not the reference.

# Understand the output

## Let's take EC as an example
- EC_predicted.tsv.gz: this is a N(# of ASV) * M(# of EC predicted) matrix.
- EC_metagenome_out/pred_metagenome_contrib.tsv.gz: this is the long form of the taxon_abun/taxon_rel_abun/genome_function_count/taxon_function_abun/taxon_rel_function_abun/norm_taxon_function_contrib, therefore the # of rows is L * N * M.
	- taxon_abun: this is the raw number from the otu table. 
	- taxon_rel_abun: This is the same as the "taxon_abun" column, but in terms of relative abundance (so that the sum of all taxa abundances per sample is 100). Note that this is relative abundance of a taxon (ASV) per function (EC #) and sample.
	- genome_function_count: Predicted copy number of this function per taxon. Since each ASV was assgined a taxon, and we have the whole genome of that taxon, and it can have multiple numbers of the same function (EC #).
	- taxon_function_abun: Multiplication of "taxon_abun" column by "genome_function_count" column.
	- taxon_rel_function_abun: Multiplication of "taxon_rel_abun" column by "genome_function_count" column. Note that this is not bounded by 100 anymore. This column is not very helpful, see next one.
	- norm_taxon_function_contrib: Proportional relative abundance of the taxon_function_abun column per function and sample. I.e., it is what proportion of the specified function is contributed by that particular taxon in the sample. This is actual taxon_rel_function_abun. This column sums up to 1 (not 100 weirdly).

- EC_metagenome_out/pred_metagenome_unstrat.tsv.gz: unstratified EC number metagenome predictions. This is a M * L(# of samples) matrix. The number in each cell the sum of taxon_function_abun of each EC in each sample.
- EC_metagenome_out/seqtab_norm.tsv.gz: otu table normalized by predicted 16S copy number abundances. Because different organisms contain different 16S gene copy numbers (GCNs), sequence variant counts are biased towards clades with greater GCNs. Therefore this is usually considered more accurate than the input otu table (raw call). The GCNs info is stored at picrust2-2.5.0/picrust2/default_files/prokaryotic/16S.txt.gz. The table has nothing to do with function prediction. This file is identical to KO_metagenome_out/seqtab_norm.tsv.gz.
- EC_metagenome_out/weighted_nsti.tsv.gz: per-sample NSTI values weighted by the abundance of each ASV. The nearest-sequenced taxon index (NSTI) will be calculated for each input ASV and by default any ASVs with NSTI > 2 will be excluded from the output.

	[NSTI](10.1038/nbt.2676) : the Nearest Sequenced Taxon Index (NSTI) quantifies the availability of nearby genome representatives for each microbiome sample (Methods). NSTI is the sum of phylogenetic distances for each organism in the OTU table to its nearest sequenced reference genome, measured in terms of substitutions per site in the 16S rRNA gene and weighted by the frequency of that organism in the OTU table. The accuracy of PICRUSt in general decreased with increasing NSTI therefore any ASVs with NSTI > 2 are excluded from the output by default.

One thing to watch out for is that if you want to input this table of ASV contributions (pred_metagenome_contrib.tsv) to another tool you should not use the --min_reads and --min_samples option to collapse rare ASVs to the RARE category since treating rare taxa as a single taxon would likely be inappropriate for many analyses (this will not happen by default).

This filetype was recently added to PICRUSt2 and is similar to the input required for several programs like BURRITO and MIMOSA. These tools expect the legacy format of this table output by PICRUSt1. To generate this file in legacy format you can run:

	convert_table.py EC_metagenome_out/pred_metagenome_contrib.tsv.gz \
                 -c contrib_to_legacy \
                 -o EC_metagenome_out/pred_metagenome_contrib.legacy.tsv.gz

## pathways_out

Folder containing predicted pathway abundances and coverages per-sample, based on predicted EC number abundances.

## How about the KEGG pathways? 

	"Note that KEGG pathways are not supported since KEGG is a closed-source database, but you can input custom pathway mapfiles if you have access. If you are using a custom function database did you mean to set the --no-regroup flag and/or change the default pathways mapfile used?"

However the mapping file from KO numbers to KEGG pathways are provided in `picrust2-2.5.0/picrust2/default_files/pathway_mapfiles/KEGG_*_to_KO.tsv`. Enriched can be done using [ClusterProfiler](http://yulab-smu.top/biomedical-knowledge-mining-book/clusterprofiler-kegg.html) fro the Yu Lab. [KASS](https://www.genome.jp/kegg/kaas/) is another option but it starts with fasta, seem to be a waste for redoing the KO assignment. 

## Additional output files

- intermediate: Folder containing intermediate MinPath files and files used for sequence placement pipeline (including JPLACE file: intermediate/place_seqs/epa_out/epa_result.jplace).
- KO_predicted.tsv.gz: As EC_predicted.tsv.gz above, but for KO predictions.
- marker_nsti_predicted.tsv.gz: Predicted 16S copy numbers and NSTI per ASV.
- out.tre: Tree of reference and study 16S sequences.

# Extra steps

## Add descriptions

`add_descriptions.py` is a convenience script that will add a column to your table of gene family or pathway abundances corresponding to a quick description of each functional category. These descriptions are in `~/build/picrust2/default_files/description_mapfiles`. You can also use custom mapping files.

To add a description column to E.C. number, KO, and MetaCyc pathway abundance tables respectively:

	add_descriptions.py -i EC_metagenome_out/pred_metagenome_unstrat.tsv.gz -m EC \
                    -o EC_metagenome_out/pred_metagenome_unstrat_descrip.tsv.gz

	add_descriptions.py -i KO_metagenome_out/pred_metagenome_unstrat.tsv.gz -m KO \
                    -o KO_metagenome_out/pred_metagenome_unstrat_descrip.tsv.gz

	add_descriptions.py -i pathways_out/path_abun_unstrat.tsv.gz -m METACYC \
                    -o pathways_out/path_abun_unstrat_descrip.tsv.gz
 
# Summary
 
 1. Input file preparing: the taxonomy column needs to be removed from the otu abundance table. the fasta file might need to be rc.
 2. The stratified table is informative and does not take much longer to generate.
 3. KEGG pathway mapping is not provided. I will share my code later.
 4. seqtab_norm.tsv should be used for composition ordination even if no functional prediction is needed since it corrects for 16S gene copy numbers. 
 

