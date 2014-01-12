---
layout: post
title: "Project Euler - Problem 1"
date: 2013-10-13 21:03
comments: true
categories: euler
---

Today I started to solve the Project Euler problems. I don't know how much time I can spend solving these problems, but I hope to complete the _quest_ in the span of a lifetime :).

So here is the first problem:

{% blockquote Problem 1 - Project Euler %}
If we list all the natural numbers below 10 that are multiples of 3 and 5, we get 3, 5, 6, 9. The sum of these multiples is 23.

Find the sum of all the multiples of 3 and 5 below 1000.
{% endblockquote %}
<!--more-->

The first try I could think of, was the obvious brute force approach. Cycle through all the numbers, and sum them up if they are multiples of 3 or 5:

```
sum = 0
for n in 1..1000
    if n % 3 == 0 || n % 5 == 0 then
        sum += n
```

This worked but I wasn't too satisfied: it was way too simple. So I started to think of a possible alternate solution. 

I thought to loop through the multiples of 3 and 5 at the same time, having two variables to hold the partial result. Something like:

```
n3 = n5 = 0
sum = 0
while n3 < 1000
    n3 += 3
    n5 += 5
    sum += n3 + n5
```

Well, in my defense, it seemed like a good idea...but it misses completely the solution. Time to move on.

What would you do when you have to think about a problem? The answer is obvious: take a shower. And it worked! 

## The shower solution

So what does the shower told me? Let's assume that we have to find the sum of all the multiples of 3 and 5 below 20, to keep the math simple.

These multiples are:

```
3+5+6+9+10+12+15+18
```
that sum up to 78. Let's split them up in multiples of 3 and multiples of 5:

```
Multiples of 3: 
3+6+9+12+15+18 = 63

Multiples of 5: 
5+10+15 = 30
```
But we could look at those like:

```
3 * (1+2+3+4+5+6) = 63

5 * (1+2+3) = 30
```
Start to see the pattern? If we could just find the sum of the first `N` natural numbers, we could find the sum just with simple multiplications. But we can! Here's how:

```
Sum of the first N numbers

N*(N+1)/2
```

So we could write:

```
3 * 6 * 7 / 2 = 63

5 * 3 * 4 / 2 = 30
```
## Problems, always problems

I hear you: _"But 63+30 isn't 78!"_. Right, where is the problem? The problem is that we are including the number `15` twice. We have to subtract this number from the total to have the correct solution:

```
3 * 6 * 7 / 2  = 63
                 +
5 * 3 * 4 / 2  = 30
                 -
15 * 1 * 2 / 2 = 15
               ------
                 78
```                 

Why is this? Because 15 = 5 * 3, so we have to consider this number and all its multiples only once (as it should count as a multiple of 3 **or** a multiple of 5).

Now we are very close to the alternate solution: there is just one more thing to consider: how do we get `N` to calculate the first `N` numbers? In our example we found the sum for the first 6 numbers for 3, the first 3 ones for 5 and 1 for 15. How did we find these values?

```
3 * (1+2+3+4+5+6) = 63 
                    +   
5 * (1+2+3)       = 30  
                    -   
15 * 1            = 15  
                  ------
                    78  
```

It's very simple: we just have to divide the max value for the number and take the integer part of the result.

```
20 / 3 = 6

20 / 5 = 4

20 / 15 = 1
```

In the case of 5, we have to decrement by 1, because the max value is out of the range, that's why we got 3 instead of 4.

## Alternate solution

So here is the alternate solution:

```

n3 = 1000 / 3
n5 = (1000 / 5) - 1
n15 = (1000 / 15) 

sum3 = 3 * n3 * (n3 + 1) / 2
sum5 = 5 * n5 * (n5 + 1) / 2
sum15 = 15 * n15 * (n15 + 1) / 2

sum = sum3 + sum5 - sum15

```


