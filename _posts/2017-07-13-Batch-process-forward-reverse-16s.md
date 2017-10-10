---
layout: post
title:  Batch process forward and reverse 16s sanger sequences
categories: [notes]
tags: [Open lab note - Sanger sequencing]
---

Even after working with sequences for about a decade, this is my first time working with sanger sequencing. In my lab, we isolate bacteria from various sources and would try to identify them afterwards by sequence their 16s, a universal marker gene for bacteria. My labmates do this on a pretty frequent basis, and each of them has dealt hundreds, if not thousands of those sequences. However they still process them one by one, which is not what I wanted to do.

The sequencing files we got from our on campus biotech center is in ab1 format, which is usually referred to as a "trace file". It contains the chromatogram info, and can be converted into fastq file, but no quality score were applied.

It took me a while to realize that Phred is the only tool to compute the Phred score (widely accepted quality score) because it is patented. Fortunately they let the scientific community use it for free. So I wrote to them and got a copy. Also in the end you need to merge the forward and reverse sequences. It is basically a alignment process. Here I choose to use the make.contigs in the Mothur package. 

The pipeline looks like:  
__ab1 files --_Phred_--> seq and qual files --_seq\_qual2trimmed\_fastq.py_--> trimmed fastq files --_Mothur_--> merged fasta file with qual file__ 

## Impatiant myceto users from the __Currie lab__:  
Make sure your ab1 sequences are renamed in the format:
strainID\_F.ab1 or strainID_R.ab1. The strainID part could be anything as long as they match each other within a pair. Then login to myceto 
	
	# defaul setting (quality cutoff = 10, consecutive window = 6)
	sh /opt/scripts/16s.sh ab1_dir 
	# user provided parameters, e.g. quality cutoff = 12, consecutive window = 8)
	sh /opt/scripts/16s.sh ab1_dir 12 8
	# Other numbers of parameters are illegal.
	
This will give you a fasta file called ab1_dir.trim.contigs.fasta, with all the merged 16s sequence for each sample. Now you could do a remote blast to see what they are. For example:  

	blastn -query ab1_dir.trim.contigs.fasta -db nt -out Donny.out -remote -outfmt "6 qacc saccver pident sscinames length mismatch gapopen evalue bitscore"

## Others:

### 1. Quality Base Calling using phred
[How to get](http://www.phrap.org/consed/consed.html#howToGet) phred if you work for a government or educational institute. Go to the second half of the post if you don't.  
__Input__: directory containing ab1 files  
__Output__: .seq(or fasta) and .qual files  
__Tool__: phred  
__Command example__: 

	PHRED_PARAMETER_FILE=/home/hfan/build/phred/phredpar.dat  
	export PHRED_PARAMETER_FILE  
	phred -id ab1_dir -sd ab1_dir1 -qd ab1_dir  

### 2. fastq and trimming
In this step we combine the fasta(.seq) and quality score(.qual) files into one fastq file and do some basic trimming using [inhouse python script](https://github.com/fanhuan/script/blob/master/seq_qual2trimmed_fastq.py).  
__Input__: directory containing .seq and .qual files  
__Output__: trimmed fastq files   
__Tool__: seq_qual2trimmed_fastq.py  
__Command example__: 

	python seq\_qual2trimmed\_fastq.py ab1\_dir 

### 3. merge forward and reverse strand.
Here I use [mothur](https://www.mothur.org/) (lazy, didn't wanna do the alignment by myself). a ab1_dir.namefile should have been generated from the previous step.

__Input__: trimmed fastq files and ab1_dir.namefile  
__Output__: ab1_dir.trim.contigs.fasta  
__Tool__: mothur  
__Command example__:

	mothur "#make.contigs(file=ab1_dir.namefile,format=sanger)"
	
## People without Phred

You can use Staden instead. It is GUI but it support batch processing and trimming.

### Install [Staden] (https://sourceforge.net/project/showfiles.php?group_id=100316)

Base calling, possibly trimming, and convert ab1 file to zrt files.

### Use bioperl to convert zrt to fastq
Install [bioperl](http://bioperl.org/INSTALL.html).
You also need to install [bioperl-ext](https://github.com/bioperl/bioperl-ext) for its Bio::SeqIO::staden::read extension. Follow all the instructions!!! Note: "It is only useful as a helper module for the SeqIO system to read sequence trace files handled by the Staden package's io\_lib "read" library. You should have this library installed prior to installing Bio::SeqIO::staden::read". I'm sure you've noticed some of the folders are 15 years old! This shouldn't be possible as "Development of the [GitHub](https://en.wikipedia.org/wiki/GitHub) platform began on 1 October 2007." and git itself only started on 2005.  
Short answer:  
1. sudo cpanm Inline::MakeMaker  
2. sudo cpanm Inline::C  
3. git clone https://github.com/bioperl/bioperl-ext  
4. install the [io\_lib](https://sourceforge.net/projects/staden/files/io_lib/) from the Staden package. Note that "Currently, the Bio::SeqIO::staden::read extension only works with io_lib v. 1.8.11 or 1.8.12; v. 1.9 and higher have several prototype changes in the read library which have broken compatibility in those versions for now" so make sure you have the right version. Again, how outdated sanger sequencing is! Download the tar and follow the README within the tar.  
   
	i. ./configure

"./configure --help" will give a list of the options for GNU autoconf. For
modifying the compiler options or flags you may wish to redefine the CC or
CFLAGS variable.

Eg (in sh or bash):
   CC=cc CFLAGS=-g ./configure

	ii. make (or gmake)

This will build the sources.

	iii. sudo make install
	
The default installation location is /usr/local/bin and /usr/local/lib. These
can be changed with the --prefix option to "configure".   
	
	iv. sudo cp config.h os.h /usr/local/include/io_lib/
	v. $cd bioperl-ext/Bio/SeqIO/staden
	$perl Makefile.PL IOLIB_LIB=/usr/local/lib IOLIB_INC=/usr/local/include/io_lib
	$make
	$make test
	$make install (probably need root permissions)
	
If there is problem locating libread.so during `make test`, try `$sudo ln -s /usr/local/lib/libread.so /usr/lib/`

### Convert zrt to fastq 
[x2y.pl](http://bioperl.org/howtos/SeqIO_HOWTO.html) is a perl that can convert between formats recognized by BioPerl. Note that there are more formats accepted in BioPerl than BioPython (what else made you think I would use Perl rather than Python?)

	
	for file in *.ztr; do name=$(basename "$file" .ztr); perl x2y.pl $file ztr $name.fastq fastq; done

### Trim and merge
Get the script [trimFastq4mothur.py](https://github.com/fanhuan/script/blob/master/trimFastq4mothur.py)
Get [mothur](https://www.mothur.org/)
	
	python trimFastq4mothur.py fastq_dir
	mothur "#make.contigs(file=fastq_dir.namefile,format=sanger)"
	 