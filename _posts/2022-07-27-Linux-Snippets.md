---
layout: post
title: Code Snippets
categories: [notes]
tags: [bash, python, R]
---

# bash
1. Loop through lines in a file

		while read p; do
		  echo "$p"
		done <peptides.txt
		
# python
1. handle exceptions

		while True:
		...     try:
		...         x = int(input("Please enter a number: "))
		...         break
		...     except ValueError:
		...         print("Oops!  That was no valid number.  Try again...")
## string

1. replace() method

		txt = "I like bananas"

		x = txt.replace("bananas", "apples")
		
## list

1. list comprehension

		[v for v in my_list for _ in range(3)]
## dictionary
1. delete a key from a dictionary

		a_dict.pop('John')
		
2. subset a dictionary

		myDict = {key:val for key, val in myDict.items() if val != 42}

## pandas
1. split a column into multiple
	
		user_df['name'].str.split(pat = ' ', expand = True)
		
2. drop duplicated rows

		df.drop_duplicates()
		
3. selected rows based on a condition

		new = df[~df.filter(regex='^node').isin([3,7,18]).any(1)]
		df.drop(df[df.score < 50].index)

		
# R
1. subset data to contain only columns whose names match a condition

		df[ , grepl( "ABC" , names( df ) ) ]	
	
	
## ggplot2

	p2 = plot_ordination(GP1, GP.ord, type="samples", color="SampleType", shape="human") 
	p2 + geom_polygon(aes(fill=SampleType)) + geom_point(size=5) + ggtitle("samples")