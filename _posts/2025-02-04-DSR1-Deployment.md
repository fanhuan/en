---
layout: post
title: DeepSeek R1 Deployment
categories: [notes]
tags: [ML]
---

OK everybody is talking about deepseek and I wanted to see for myself. 

I am following the note from [Xihan Li](https://snowkylin.github.io/blogs/a-note-on-deepseek-r1.html).

Specs of my desktop. 
- CPU(`free -h`): 128G
- GPU(`lspci | grep -i vga`): two RTX A4500, each has 20 GB of GDDR6 memory.

I guess I will try the smallest one (1.58-bit, 131GB).

_____________________________________________________________________________________________________

That was Feb 4th, 2025. Today is Feb 27, 2025, and I have discovered [LM Studio](https://lmstudio.ai/), end of story... 

OK a few more tips:

1. Add the `--no-sandbox` flag to bypass the sandbox requirement (use cautiously, as this reduces security) if you run into the SUID sandbox error.
2. Remember to eject the model when you are done running it to free up the memory.

Who would know that one day when you say __LM__ it refers to __language models__ (or __large models?__) instead of __linear models__...

Have fun! 