---
layout: post
title:  How To Store Your Data In A Dataframe
categories: [notes]
tags: [R]
---
	results <-data.frame(ID=character(),e=double(),'0'=integer(),'1'=integer(),'2'=integer(),stringsAsFactors=FALSE)
	result<-c(strsplit(basename(file),'_')[[1]][1],0.0005,sum(unlist(dm1_ds0[,-35])==0),sum(unlist(dm1_ds0[,-35])==1),sum(unlist(dm1_ds0[,-35])==2))
	results[nrow(results)+1,]<-result

