---
layout: post
title:  zcat vs gzcat
categories: [notes]
tags: [Open lab note - OS X]
---
http://stackoverflow.com/questions/19425857/env-python-r-no-such-file-or-directory

with open('beak', 'rb+') as f:
    content = f.read()
    f.seek(0)
    f.write(content.replace(b'\r', b''))
    f.truncate()