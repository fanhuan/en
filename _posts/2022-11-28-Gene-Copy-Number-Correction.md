---
layout: post
title: 16S otu table corrected for gene copy number
categories: [analysis]
tags: [amplicon, microbiome]
---

Apparently I did gene copy number (gcn) correction for the otu table that I got from the sequencing company back in July. However, I had no records of how I did it! All my scripts in R started with this otu table with gcn in it, suggetting that is has been corrected, but no trace of how those tables were generated in the first place. 

I believe it has something to do with PICRUST2, and I did found this in [my previous post](https://fanhuan.github.io/en/2022/07/05/Picrust2-basic/):

	Because different organisms contain different 16S gene copy numbers (GCNs), sequence variant counts are biased towards clades with greater GCNs. Therefore this is usually considered more accurate than the input otu table (raw call). The GCNs info is stored at picrust2-2.5.0/picrust2/default_files/prokaryotic/16S.txt.gz. The table has nothing to do with function prediction. This file is identical to KO_metagenome_out/seqtab_norm.tsv.gz.
	
A good guess would be that it is automatically corrected as part of PICRUST2. Is it? The only way to test, is to re-run the PICRUST2 for my newly merged phyloseq from fastq data.

While reading my posts on the installation of PICRUST2, the horrible memory of [lossing my base conda](https://fanhuan.github.io/en/2022/07/19/Conda-Environment-Went-Crazy/) environment came back. But that post is incomplete, and all I can remember was that the crazyness was caused by installing PICRUST2 within another virtrual environment (probably biobakery3) and I had to fix the path for bas conda env in the .bashrc or .bashprofile. Please do not do such a stupid thing.

## Re-installing picrust2

However I was not able to activate my picrust2 env successuflly.

	$ mamba activate picrust2
	ModuleNotFoundError: No module named 'conda._vendor.auxlib'

Apparently this is a mamba version problem (too old/low). My `conda`, `mamba` and `python3` are all in the same place under the anaconda3/bin. 

According to [this post](https://github.com/mamba-org/mamba/issues/1583), updating `conda-build` might help. 

	$ conda update conda-build
	Collecting package metadata (repodata.json): done
	Solving environment: |\
	
	
But then it just hang there forever (like a day!). Well, maybe it is time to finally learn what conda means when it is "Solving environment".

The first thing I learnt is that Conda is an environment management infrastructure, and anaconda is a large(r) working environment. If you keep install things to the base environment, it's going to become hard to maintain, including solving the compatability between packages. Therefore it is wiser to install different packages under different environment. I wish R has things like this so we do not need to deal with functions with the same name but from different packages. 

According to [this post](https://technicqa.com/how-does-conda-install-hang-at-solving-environment/), if we have the most recent version of conda installed, it only takes 1-2 min to solve the environment. 20+ min would already be too long. 

	$ conda config --show-sources
	==> /home/user/.condarc <==
	channels:
	  - conda-forge
	  - bioconda
	  - defaults
	  - r
	unsatisfiable_hints: True

Some suggested removing conda-forge from the channels. I started at 15:07. I will give it 20min. 
 
Another suggestion is to try adding –no-channel-priority to the command. This one helped me a couple times since updating to Conda 4.6. When conda seem stuck it is possibly having too many options or some conflicts resulting in the SAT solver getting clause counts of multiple millions.

In a [conda issue](https://github.com/conda/conda/issues/7690)

suggestions include:
1. Dropping defaults and just using "main" seems to have helped.

	$ conda config --override-channels -c main -c conda-forge 
	
	$ conda config --set channel_priority strict
	
2. Used the conda navigator instead: anaconda-navigator

3. recommend users never put both Python and R in the same/base env or channel (which I already did).

OK, it seems like I've done too many stupid things and I will just install miniconda from scratch. There are two ways of [uninstalling anaconda distribution](https://docs.anaconda.com/anaconda/install/uninstall). The first way removes all traces of the configuration files and directories from Anaconda and its programs with the `anaconda-clean` program.  

	$ conda install anaconda-clean
	Collecting package metadata (repodata.json): done
	Solving environment: | 
	The environment is inconsistent, please check the package plan carefully
	The following packages are causing the inconsistency:
	...
	Done

But the installation still failed.

	Verifying transaction: failed

	RemoveError: 'requests' is a dependency of conda and cannot be removed from
	conda's operating environment.
	RemoveError: 'ruamel_yaml' is a dependency of conda and cannot be removed from
	conda's operating environment.
	RemoveError: 'setuptools' is a dependency of conda and cannot be removed from
	conda's operating environment.

This time it took less than 1min to solve the environment. I wonder why `conda update conda-build` takes so long so I rerun it. This time it did not hang. So apparently deleting conda-forge from the channels helped, just not right away for some reason. But this time it hangs before giving a package plan `__add_sys_prefix_to_path; "$CONDA_EXE" $_CE_M $_CE_CONDA "$@" `

Had to do a simple remove then. Remember to remove the initiations from your `.bashrc` or `.bash_profile`.

This time I will install [miniconda](https://docs.conda.io/en/latest/miniconda.html) instead. The architecture of my desktop is AMD64 aka x86_64 (`uname -a`) so I downloaded [this one](https://repo.anaconda.com/miniconda/Miniconda3-py39_4.12.0-Linux-x86_64.sh) 

now install picrust2 without tizi:

	$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
	hfan@hfan-Precision-7820-Tower:~/Documents/Projects/FicusMeta$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
	$ conda create -n picrust2 -c bioconda -c conda-forge picrust2=2.5.1
	Collecting package metadata (current_repodata.json): done
	Solving environment: failed with repodata from current_repodata.json, will retry with next repodata source.
	Collecting package metadata (repodata.json): done
	Solving environment: | - ^Z
	[3]+  Stopped                 ( __add_sys_prefix_to_path; "$CONDA_EXE" $_CE_M $_CE_CONDA "$@" )

Again this hang for a long time. Maybe I just need to restart my PC?

with tizi: 

	$ conda create -n picrust2 -c bioconda -c conda-forge picrust2=2.5.1
	Collecting package metadata (current_repodata.json): failed

	ProxyError: Conda cannot proceed due to an error in your proxy configuration.
	Check for typos and other configuration errors in any '.netrc' file in your home directory,
	any environment variables ending in '_PROXY', and any other system-wide proxy
	configuration settings.

But my tizi is not stabl recently.  Now let's 

Install [mamba](https://github.com/conda-forge/miniforge#mambaforge) by downloading Mambaforge. 

The install using mamba like documented in this post.

	$mamba create -n picrust2 -c bioconda -c conda-forge picrust2=2.5.1
	
Success! And I see the mirror addresses was at work. I was also able to actiavet the picrust2 environment by:

	$ mamba activate picrust2
	
## Running picrust2 on ASVs generated from fastq data

### Input

- fasta of amplicon sequences variants (study_seqs.fna)
This can be generated in R by `writeXStringSet(refseq(ps), 'test.fasta', append=FALSE, compress=FALSE, compression_level=NA, format="fasta")`

- biom table of the abundance of each ASV across each sample (study_seqs.biom). I don't have this. A tab-delimited table with ASV ids as the first column and sample abundances as all subsequent columns will also work. I will use the latter.

This is basically the otu table. `write.table(otu_table(ps),'test.otu_table.txt', sep = '\t', quote = FALSE)` Since the ASV ids are in the rownames, you will need to add its column names manually (say with vi).

OK now run picrust2:

		$picrust2_pipeline.py -s test.fasta -i test.otu_table.txt -o test -p 12 --stratified

Again we run into the reverse-compliment problem since the input study sequences need to be on the positive strand. This time I also ran into a problem where the sequence from one record spread into multiple lines (line breaks) but I've already updated the complement.py script.

		$ python ~/GitHub/script/complement.py -i test_rep.fna -o test_rep_rc.fna
		
		$ add_descriptions.py -i pathways_out/path_abun_unstrat.tsv.gz -m METACYC \
                    -o pathways_out/path_abun_unstrat_descrip.tsv.gz
                    
# GCN correction.
The corrected otu table is this:

- EC_metagenome_out/seqtab_norm.tsv.gz: otu table normalized by predicted 16S copy number abundances. Because different organisms contain different 16S gene copy numbers (GCNs), sequence variant counts are biased towards clades with greater GCNs. Therefore this is usually considered more accurate than the input otu table (raw call). The GCNs info is stored at picrust2-2.5.0/picrust2/default_files/prokaryotic/16S.txt.gz. The table has nothing to do with function prediction. This file is identical to KO_metagenome_out/seqtab_norm.tsv.gz.

## Should I tackle KEGG pathway this time?

MetaCyc is supposed to be a good representative for bacteria. However, there are >5000 pathways and it is hard to comprehend. It is possible to summarize based on its [tree structure](https://metacyc.org/META/class-tree?object=Pathways). But I haven't found existing tools. A bit surprised that it is not included in the HUMAnN 3.0 pipeline.

Then I talked to Qiang and he told me about FAPROTAX, which gives a more aggregated functional annotation. I will give it a try. However, picrust2 will still be used for gcn correction.

I will have a separate post for FAPROTAX.




 









 