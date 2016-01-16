---
layout: post
title:  All the applies
categories: [notes]
tags: [Open lab note - R]
---

I just learnt that for loop is super inefficient in R. You should use for loops only if your next iteration depends on the previous one. If you just want to do something to a list of things seperately, you should NOT use a for loop.

But what should you use then? There is a school of functions called .apply$. There are lapply, sapply, FIXTHIS, etc. They are more efficient than a for loop, but as you might be suspecting, they are very confusing. 


