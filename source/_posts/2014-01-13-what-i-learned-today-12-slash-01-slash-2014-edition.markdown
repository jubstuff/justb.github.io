---
layout: post
title: "What I learned today - 12/01/2014 edition"
date: 2014-01-13 00:36:12 +0100
comments: true
categories: wilt
---

Today was a strange day. I took the resolution to install again Linux on my work laptop, after a year or so of _Windows only_. 
"Why did you do **that**?" you could ask. I don't know. 

Maybe because I needed Photoshop for my previous work. I don't know.
I've learned much, though. I've pushed the Windows development environment to the limit but, simple as that, I'm not a Windows guy.

Just two minutes into my Linux environment and I felt home. 

Anyway, what I learned today? (that's a rhyme!)

<!--more-->
### Accept merge conflicts as a whole (local/remote)

It's as simple as 

```
git checkout --theirs /path/to/file
```

or

```
git checkout --ours /path/to/file
```

[Source][1]

### Executing a command in Puppet in a specific directory

Do you want to execute a command with puppet, but you need it to run from a specific folder?

```
exec { command:
  cwd => "path/to/folder"
}
```

[Puppet exec documentation][2]

## Sites, Apps, Services, Videos

 * [Bespoke.js][bespoke]: a wonderful presentation framework written in JavaScript.
 * [Node.js explained][nodeexp]: an introduction to Node.js. I really liked it! Very concise, but also informative.
 * [apiDoc][apidoc]: npm package to generate documentation for RESTful web APIs.
 * [Build podcasts][buildpod]: an on-going series of podcasts on web development tools.


[1]: http://stackoverflow.com/questions/6650215/how-to-keep-the-local-file-or-the-remote-file-during-merge-using-git-and-the-com
[2]: http://docs.puppetlabs.com/references/latest/type.html#exec
[bespoke]: http://markdalgleish.com/projects/bespoke.js/
[nodeexp]: http://www.youtube.com/watch?v=L0pjVcIsU6A
[apidoc]: http://apidocjs.com/
[buildpod]: http://build-podcast.com/
