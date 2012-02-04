---
layout: post
title: "Tom and Michael vs. John Von Neumann"
date: 2011-10-23 19:55
comments: true
categories:
  - papers
  - programming
  - versus
---
In case you wonder if [Tom](http://mrjaba.posterous.com/) and I gave up on [reading papers](http://citizen428.net/blog/categories/versus/), the answer is a resounding no! In fact there's one we finished sometime around mid-September, which for various reasons hasn't been summarized yet. This is about to change.

**The paper**

The paper in questions was ["Can Programming Be Liberated from the von Neumann Style?"](http://www.stanford.edu/class/cs242/readings/backus.pdf), a [Turing Award](http://en.wikipedia.org/wiki/Turing_Award) lecture by [John Backus](http://en.wikipedia.org/wiki/John_Backus).

** A very brief summary**

At the core of his lecture, Backus argues in favor of functional/applicative programming. This is done by first exploring shortcomings and bottlenecks in the [von Neumann architecture](http://en.wikipedia.org/wiki/Von_Neumann_architecture), followed by an exploration of concepts from his own [FP](http://en.wikipedia.org/wiki/FP_(programming_language)) (Function Programming) language.


** Difficulty of the paper**

Generally this is very easy to read, and provides good food for thought, even today. The more mathematical parts in the middle might deter some readers, but aren't really that important.

**Takeaway**

Alas the age of the paper shows a bit. While some of the points are still valid, there undoubtedly is a much wider appreciation of the benefits of applicative style programming nowadays, as can be seen by various functional languages slowly finding their way into the mainstream. It's still very interesting to see how early Backus already thought about all of this though, and also that the language he envisioned seemed to be more in line with the [APL](http://en.wikipedia.org/wiki/Apl) school of thought that e.g. Haskell, which in all fairness wasn't around back then. I wonder how Backus felt about the latter,  which can be very declarative (see [pointfree style](http://www.haskell.org/haskellwiki/Pointfree)). For example, here is the inner product function from section 5 of the paper translated to Haskell:

{% codeblock lang:haskell %}
ip :: [[Integer]] -> Integer
ip = sum . map product . transpose
{% endcodeblock %}

If you like programming languages and also like to think about them, and learn about their history, this is an interesting paper to read. Also it's quite ironic that Backus actually won in large parts for his contributions to Fortran, a language that influenced so many other imperative languages.
