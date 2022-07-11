---
layout: post
title: Running Picrust2 - Advanced 
categories: [notes]
tags: [python, amplicon-sequencing]
---

## Running partial pipeline

This happens when you want to enrich for the metacyc pathways using only part of the ASVs. 

First we need to prepare a subset of pred_metagenome_contrib file for EC.

This can be done in R or python, but since the original file is usually big, can this be done in bash instead?

	$awk -F'\t' '$3 == "ASV_N"' pred_metagenome_contrib.tsv >subset_pred_metagenome_contrib.tsv
	
This works for a string ("ASV_N") but when I pass a file in, somehow awk does not do the job anymore. What I tried was:

	$ while read p 
	do 
		awk -F'\t' '$3 == $p' pred_metagenome_contrib.tsv >> pred_metagenome_contrib_subset.tsv
	done < ASV_list.txt

But for some reason this does not work. I had to write a [python script](https://github.com/fanhuan/script/blob/master/grep_by_column.py) to do this:

	python ~/GitHub/script/grep_by_column.py -1 pred_metagenome_contrib.tsv -2 ASV_list.txt -l 3 > pred_metagenome_contrib_subset.tsv 
	
__Note__ that the header of pred_metagenome_contrib.tsv needs to be added to pred_metagenome_contrib_subset.tsv before running the next script.

Then we reconstruct the pathways:

	$ pathway_pipeline.py -i $OUT_META/pred_metagenome_contrib_subset.tsv \
		                 -o out_folder \
		                 -p 12
                     
## Generating shuffled predictions
This is a way to test the robustness of the predictions by compare the PICRUSt2 output tables with tables based on shuffling the predictions across all amplicon sequence variants (ASVs), a sort of permuation. The script is called `shuffle_predictions.py`; it randomizes the ASV labels for all predicted genomes (so all the same individual predicted genomes are the same - they just are linked to different ASV abundances across samples).

	shuffle_predictions.py -i EC_predicted.tsv.gz \
                           -o EC_predicted_shuffled \
                           -r 5 \
                           -s 131

Where -r specifies how many random replicates to make and -s 131 specifies a random seed so that the same shuffled tables will be output reproducibly if this seed were used again.

The gene family and pathway-level prediction tables can then be generated from these shuffled tables by running the standard PICRUSt2 commands. Below is an example of how to quickly run metagenome_pipeline.py and pathway_pipeline.py on all shuffled tables with a bash loop.

	# Make folders for shuffled output
	mkdir EC_metagenome_out_shuffled
	mkdir pathways_out_shuffled

	for i in {1..5}; do
	    
	    # Define in and out file paths.
	    EC_SHUFFLED="EC_predicted_shuffled/EC_predicted_shuf"$i".tsv.gz"
	    OUT_META="EC_metagenome_out_shuffled/rep"$i
	    OUT_PATHWAYS="pathways_out_shuffled/rep"$i
	    
	    # PICRUSt2 scripts to get prediction abundance tables for gene and pathway levels, respectively.
	    # note that this otu table again cannot have taxaonomy column
	    metagenome_pipeline.py -i otu_table_total.txt -m marker_predicted_and_nsti.tsv.gz -f $EC_SHUFFLED \
		               -o $OUT_META \
		               --strat_out
	    
	     pathway_pipeline.py -i $OUT_META/pred_metagenome_contrib.tsv.gz \
		                 -o $OUT_PATHWAYS \
		                 -p 12
	done   
	 
 Note that sh does not recognize {1..5}, this script needs to be run with bash 本 ba.
 
 These shuffled tables are especially helpful to get a baseline for how the predicted functional data differentiates samples (e.g. based on ordination or differential abundance testing) when the predicted ASV genomes are assigned randomly.
 
 The ordination etc will be done in R (I used phyloseq). 
 
 ## Summary
 
 1. Use bash, not sh
 2. Note that the otu table required by picrust cannot have taxaonomy column.

 
 

