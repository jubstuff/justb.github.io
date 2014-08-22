---
layout: post
title: "Short tags, long pain"
date: 2014-08-22 22:50:28 +0200
comments: true
categories:
---

Recently I've joined a colleague of mine into a project that involves a WordPress multisite. First thing first,
I downloaded the code and the database to study the codebase in my development environment, as I usually do.

I created a `.dev` virtualhost and used wp-cli to search and replace the original domain in the WordPress database.

Then, I opened the browser and navigated to the root site of the multisite. There, I noted something strange: the title
of the website was wrong. It was taking the title from on of the child site.

I googled for _"WordPress load wrong site"_ without success, then I thought of testing the site with the Twenty Twelve
theme to check if there was something wrong with my installation. As I was thinking, with TT the site was working good.

So, there was something in the theme!

In this theme there were many loops over all the child sites, to print and link some informations in the root site.
After these loops in WordPress, you have to call the `restore_current_blog()` function, to reset the global environment
that WordPress needs to run. If you skip one of these calls, the site would act just like it was on my dev environment.

But all the loops had the call to `restore_current_blog()` at the bottom.

Then, I noted something strange; this:

```php
<? restore_current_blog(); ?>
```

I don't like short tags. I always try to make my code as portable as possible. I even had arguments in the past with some
colleagues in this regards. Given this, I always disable short tags on my servers. Even on development ones.

Needless to say, I just needed to replace the short tags with standard `<?php` to make the site work as it should.
Oh, I hate short tags!
