---
layout: post
title: "Multiple Sums with different conditions in MySQL"
date: 2012-12-17 20:52:52
comments: true
sharing: false
categories: SQL
---

Today I was working on my last project that involves some statistics on data. I had a table like this:

{% img center /images/tabella-1.png" %}

I needed the daily, monthly and yearly sum of the <code>value</code> field. How to do this?

<!-- more -->

The simplest approach was to have three different queries to retrieve the needed values:


``` sql
--day
SELECT SUM(value)
FROM table
WHERE DATE(time)=CURDATE();
-- month
SELECT SUM(value)
FROM table
WHERE MONTH(time)=MONTH(CURDATE()) AND YEAR(time)=YEAR(CURDATE());
-- year
SELECT SUM(value)
FROM table
WHERE YEAR(time)=YEAR(CURDATE());
```

But I wasn't satisfied. I wanted to have all three values using only one query. So I searched for this issue and this is the resulting query:

``` sql
SELECT
  SUM(CASE WHEN DATE(time)=CURDATE()
    THEN value
    ELSE 0 end) AS value_day,
  SUM(CASE WHEN MONTH(time)=MONTH(CURDATE()) AND YEAR(time)=YEAR(CURDATE())
    THEN value
    ELSE 0 end) AS value_month,
  SUM(CASE WHEN YEAR(time)=YEAR(CURDATE())
    THEN value
    ELSE 0 end) AS value_year
FROM table
```

Using the CASE construct, we increment independently the three values so that each row that satisfies the condition gets summed up.

