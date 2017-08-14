---
layout: post
title:  Convert, trim and merge 16s sanger sequences
categories: [notes]
tags: [Open lab note - Sanger sequencing]
---

Even after working with sequences for about a decade, this is my first time working with sanger sequencing. In my lab, we isolate bacteria from various sources and would try to identify them afterwards by sequence their 16s, a universal marker gene for bacteria. My labmates do this on a pretty frequent basis, and each of them has dealt hundreds, if not thousands of those sequences. However they still process them one by one, which is not what I wanted to do.

The sequencing files we got from our on campus biotech center is in ab1 format, which is usually referred to as a "trace file". It contains the chromatogram info, and can be converted into fastq file, but no quality score were applied.

It took me a while to realize that Phred is the only tool to compute the Phred score (widely accepted quality score) because it is patented. Fortunately they let the scientific community use it for free. So I wrote to them and got a copy. Also in the end you need to merge the forward and reverse sequences. It is basically a alignment process. Here I choose to use the make.contigs in the Mothur package. 

The pipeline looks like:  
### 1. Quality Base Calling
__Input__: ab1 files  
__Output__: .seq(or fasta) and .qual files  
__Tool__: phred

### 2. fastq and trimming
In this step we combine the fasta and quality score files into one fastq file and do some basic trimming using inhouse python script. 

System: Ubuntu 16.04.2
### Install [Staden] (https://sourceforge.net/project/showfiles.php?group_id=100316)

Convert ab1 file from biotechcenter to zrt files.
### Install [bioperl](http://bioperl.org/INSTALL.html)
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



### Universal converter
	
	for file in *.ztr; do name=$(basename "$file" .ztr); perl ~/scripts/x2y.pl $file ztr $name.fastq fastq; done
	python ~/scripts/trimFastq4mothur.py fastq_dir
	
	make.contigs(file=namefile,format=sanger)
	
Alternative:
PHRED_PARAMETER_FILE=/home/hfan/build/phred/phredpar.dat  
export PHRED_PARAMETER_FILE  
mkdir Shelby_0731_phred  
phred -id ~/Shalby_0731 -sd Shelby_0731_phred -qd   Shelby_0731_phred  
cd Shelby_0731_phred  
for name in *.seq; do python /home/hfan/scripts/  fasta_to_fastq.py $name; done  
cd ..  
python ~/scripts/trimFastq4mothur.py Shelby_0731_phred  