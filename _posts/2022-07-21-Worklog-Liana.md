---
layout: post
title: Open lab notebook - Liana mix
categories: [notes]
tags: [lab note, liana]
---

Runze wrote a script for generating sparse kmers:

 	for file in fasta/*/*.fasta; do echo $file; python ~/GitHub/Calculate_DNA_percentage/kmers_count.py -fa $file -k 11 -s 0; done
 	
 I should be able to use `kmer-merge` to merge those files except:
 1. the dimilimer of those files are ','
 2. ?
 
 A standarded `kmer_merge` command looks like:
 
 	kmer_merge -k s -c -d '0' -a 'T,M,F' '00-grape.pkdat.gz' '14-MH.pkdat.gz' '16-PA.pkdat.gz' '19-UR.pkdat.gz' '24-A886.pkdat.gz' '27-AM.pkdat.gz' '31-DS.pkdat.gz' '33-UT.pkdat.gz' '34-CV.pkdat.gz' '35-CP.pkdat.gz' '39-TC.pkdat.gz' '40-UY.pkdat.gz' '42-TC.pkdat.gz' '43-AP.pkdat.gz' '45-UH.pkdat.gz' | cut -f 1,2,4,6,8,10,12,14,16,18,20,22,24,26,28,30 | gzip >> chloroplast_BL_k11.dat.gz
 	
 options:
 -k: s for characters
 -c: The number of columns is fixed in each file
 -d: '0', default column string, otherwise NULL
 -a: how to process all files, T means twice, M means mark the content from which file in output (header), F means auto fill in key columns if not exists in this file (-d:'0').
 
 No options about delimiter. Let's try to ignore it first.
 
 	kmer_merge -k s -c -d '0' -a 'T,M,F' '00-grape_k11_s0.csv' '14-MH_k11_s0.csv' '16-PA_k11_s0.csv' '19-UR_k11_s0.csv' '24-A886_k11_s0.csv' '27-AM_k11_s0.csv' '31-DS_k11_s0.csv' '33-UT_k11_s0.csv' '34-CV_k11_s0.csv' '35-CP_k11_s0.csv' '39-TC_k11_s0.csv' '40-UY_k11_s0.csv' '42-TC_k11_s0.csv' '43-AP_k11_s0.csv' '45-UH_k11_s0.csv' | cut -f 1,2,4,6,8,10,12,14,16,18,20,22,24,26,28,30 | gzip >> chloroplast_BL_k11.dat.gz
 	
No it does not work.
OK sed first.

