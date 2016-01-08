---
layout: post
title:  Repository on multiple machine
categories: [notes]
tags: [Open lab note - Ficus RNA-seq]
---


Ierror: failed to push some refs to 'https://github.com/fanhuan/script'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
Huans-MacBook-Pro:script Huan$ git pull
remote: Counting objects: 16, done.
remote: Total 16 (delta 2), reused 2 (delta 2), pack-reused 14
Unpacking objects: 100% (16/16), done.
From https://github.com/fanhuan/script
   20639e2..1813ce5  master     -> origin/master
Auto-merging simRadAlignment.py
CONFLICT (add/add): Merge conflict in simRadAlignment.py
Auto-merging shared_diversity.py
CONFLICT (add/add): Merge conflict in shared_diversity.py
Removing prune_newick
Auto-merging filter_kmer_from_pattern.py
CONFLICT (content): Merge conflict in filter_kmer_from_pattern.py
Removing 1simulation.R
Automatic merge failed; fix conflicts and then commit the result.

