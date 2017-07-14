---
layout: post
title:  Convert abi trace file to fastq
categories: [notes]
tags: [Open lab note - Sanger sequencing]
---

System: macOS Sierra 10.12.5
### [Install bioperl](http://bioperl.org/INSTALL.html)
You also need to install [bioperl-ext](https://github.com/bioperl/bioperl-ext) for its Bio::SeqIO::staden::read extension. Note: "It is only useful as a helper module for the SeqIO system to read sequence trace files handled by the Staden package's io_lib "read" library. You should have this library installed prior to installing Bio::SeqIO::staden::read". I'm sure you've noticed some of the folders are 15 years old! This shouldn't be possible as "Development of the [GitHub](https://en.wikipedia.org/wiki/GitHub) platform began on 1 October 2007."   
Short answer:  
1. sudo cpanm Inline::MakeMaker  
2. sudo cpanm Inline::C  
3. git clone https://github.com/bioperl/bioperl-ext
### Universal converter
