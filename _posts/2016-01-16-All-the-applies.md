---
layout: post
title:  All the applies
categories: [notes]
tags: [Open lab note - R]
---

I just learnt that for loop is super inefficient in R. You should use for loops only if your next iteration depends on the previous one. If you just want to do something to a list of things independently, you should NOT use a for loop.

But what should you use then? There is a school of functions called .apply$. There are apply, lapply, sapply, etc. They are more efficient than a for loop, but as you might be suspecting, they are very confusing.

### apply(data,axe,fun)

`apply` takes three arguments, the data frame, the direction you want the function to work on, 1 for row-wise and 2 for column-wise. 

### lapply(dataframe or list(column or rows), function)

Here `l` stands for list. If you supply a dataframe, it returns a dataframe, while if you supply a list, it returns a list, very convenient. 

However there is a catch: what kind of functions can be used here. Those that take only one argument, which is the data itself, has not problem. Just make sure you don't add the parenthesis after the function name. e.g. lapply(df$col1, max). But what if we need to use a function that requires more than one argument, say strsplit?


 


