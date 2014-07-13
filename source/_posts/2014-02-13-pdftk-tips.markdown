---
layout: post
title: "Pdftk tips and commands"
date: 2014-02-13 13:20:43 +0100
comments: true
categories: tips tools
---

Often I need to manipulate in some way PDF files. On Windows, the best tool is Adobe Acrobat. But there is one downside: it costs way too much!
[Pdftk](http://www.pdflabs.com/tools/pdftk-the-pdf-toolkit/) is a wonderful command line tool that lets you manipulate PDF files. It is free and you can install it even on Windows.


Here are some useful commands that I use:

```
# buil a PDF out of different PDF files
# Syntax is: Astartpage-endpage
justb:~/pdftk
⇒ pdftk A=file1.pdf B=file2.pdf C=file3.pdf cat A1 B1 A3 C1 A5-end output output.pdf
```


