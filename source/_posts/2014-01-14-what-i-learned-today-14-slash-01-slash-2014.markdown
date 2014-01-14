---
layout: post
title: "What I learned today - 14/01/2014"
date: 2014-01-14 01:59:10 +0100
comments: true
categories: wilt
---

Today has been a good day. This morning I decided that I would begin my training as a runner. I ran for 10 minutes! (Yes, I'm unfit. Sigh.).

Anyway, this is not an health blog, but a programming one (I hope). So let's see what I learned today!

<!--more-->

### Smarty: dump variable and verbatim text

So I got this job, and I have to use Smarty. I know, I know, it's so 2000. But hey, it's work. I needed to dump some variables for a quick analysis. This is how it is done:

```smarty
{ $variable | print_r }
```

Simple enough. Next, there where some errors regarding (brr) inline JavaScript. The { } where being processed by Smarty. They needed to be passed unfiltered. This is done using the `{literal}` tag:

```smarty
{literal}
<script>
function foo() {
  console.log('bar');
}
</script>
{/literal}
```

### Copy text in the clipboard using Ruby in Linux

I created this blog using Octopress. To create a new post you have to issue `rake new_post['post title']`.
Usually the filename is some mix of date and the title you typed. After creating the post you need to open it in Vim (or your favourite editor).
So I modified the rake task to copy the filename in the clipboard, so that I don't have to type it.

```ruby
puts "Creating new post: #{filename}"
system("echo '#{filename}' | xclip")
```

You have to install the `xclip` package.

### Cancel last search highlight in Vim

I hate it! `:set nohlsearch` FTW!
