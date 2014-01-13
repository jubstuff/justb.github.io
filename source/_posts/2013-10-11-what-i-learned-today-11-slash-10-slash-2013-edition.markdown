---
layout: post
title: "What I learned today - 12/10/2013 edition"
date: 2013-10-12 15:50:03
comments: true
categories: wilt
---

##So what I learned today?

### Replace method in Python

In Python you can use the `replace` method on strings to replace a part of it with another

```
In [3]: key = '/blobstore/quhab72=qaqerqfxbhr'
In [4]: key.replace('/blobstore/', '')
Out[4]: 'quhab72=qaqerqfxbhr'
```

### Logging in AppEngine

To have your debug logs appear in the console when you are running an AppEngine app in your development environment, just start the server with:

```
$ dev_appserver.py --log_level=debug .
```


