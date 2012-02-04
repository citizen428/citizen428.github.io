---
layout: post
title: "Review: The Art of R Programming"
date: 2011-12-21 20:06
comments: true
categories:
    - review
    - books
---
*Disclaimer: Once again No Starch Press were kind enough to provide me with a review copy of a book, but this does not influence my reviews.*

Time for another book review, this time [The Art of R Programming](http://nostarch.com/artofr.htm) by Norman Matloff. First things first, this book really lives up to its name! It's a thorough introduction to programming in R, aimed at software developers. This is **not** the book for you if you want to learn about the statistics side of R or how to make prettier plots, there are plenty of books about that anyway.

What I really like is that the author never tries to "sell" R to the reader. This is rather refreshing, because I always get turned off when books start with a sales pitch for something I already clearly showed interest in by purchasing the book. R is a rather specialized language and chances are that you know why you want to solve a given problem in it, and if you don't, this might not yet be the book you should be reading anyway. The first few chapters cover the language's basic data structures like vectors, matrices, arrays, lists and tables, before chapter 7 introduces various flow control structures. What follows is a chapter on doing math and simulations in R, which is not overly long but gives some good examples of what the language is useful for. The next chapter is very interesting, it deals with R's object oriented features and describes the differences between S3 and S4 classes as well as their respective up and down sides. After this the author covers input/output, dealing with strings and R's graphing capabilities. He never gets lost in detail, but provides you with enough info to be able to explore these areas on your own. What really makes the book special though are the last few chapters, where Matloff covers debugging (a topic he also wrote an entire book about), performance tradeoffs, interfacing R with other programming languages (using functions written in C/C++ from R, as well as using R in Python) and various approaches to parallelizing R. While none of this may sound super exciting at first — apart from parallelism maybe — these are important issues in the daily lives of software developers and way too many books only gloss over them.

Now for some criticism: personally I don't find the author's style very engaging, it's rather dry and boring at times. Since it's clear that Matloff is an absolute authority on the topic of R this doesn't way too heavily, but still deserves a mention. Also for my personal taste there are slightly too many forward references in the text, although that's hard to avoid if you want to properly cover a language. I also found it rather odd that at one point the author mentions that explicit return statements aren't exactly idiomatic in R, but that he will keep using them for the benefit of readers unaccustomed to the language. Given that this was a few chapters in, it would have been a perfect spot to switch to the more idiomatic style from then on, but maybe that's just me being nitpicky. I also noticed several little typos and misspellings, something I'm not really used to from No Starch Press.

All in all this is a very solid book, which you definitely should pick up if you want to learn programming in R!
