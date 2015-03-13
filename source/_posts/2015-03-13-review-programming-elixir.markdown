---
layout: post
title: "Review: Programming Elixir"
date: 2015-03-13 22:32:51 +0700
comments: true
categories:
  - books
  - programming
  - review
  - elixir
---
_**Disclaimer**: The good folks at [The Pragmatic Bookshelf](https://pragprog.com/) were nice enough to provide me with a free copy of this book, but this has no influence on the contents of the review._

My first contact with Dave Thomas was the famous "[Pickaxe book](https://pragprog.com/book/ruby/programming-ruby)" for Ruby. It wasn't my first contact with the language, but it certainly helped in deepening my understanding of it. It's a great book, and I went back numerous times to re-read certain chapters. I already had a crush on Ruby, but "the Pickaxe" helped turning that into a solid and lasting love affair.

Fast-forward some years. While I still love Ruby and work with it professionally, I have to admit to having a new language crush: [Elixir](http://elixir-lang.org/). I've been interested in Erlang for several years, read some books, dabbled a bit, but never really got into the language. Elixir however got me hooked almost from the get-go. I find it's a simple, elegant language, that's easy to pick up, but has quite some depth.

### The book

Let's start off by clearly stating what this book is not: a reference. It weighs in at around 340 pages and does not try to cover every aspect of Elixir/Erlang. It still is one of my all-time favorite programming books though. Why? Because I believe it does an amazing job in conveying the "essence" of Elixir. There's no point in documenting APIs, the language has [great documentation online](http://elixir-lang.org/docs.html). Instead Dave tries to teach the reader how to think in Elixir.

"Programming Elixir" has three main parts. The first called "Conventional Programming" introduces some core concepts like pattern matching and immutability, before covering Elixir types, functions, modules, language constructs, and project organization. It's easy to read, but not too boring for seasoned developers. However, if you have some previous experience with Elixir, Erlang, or even some other functional programming language you may end up skimming this section a bit, at least I did.

Where it starts to get really interesting is the next part called "Concurrent Programming", which covers processes, nodes and the basics of OTP. This latter part can easily fill books of its own, so the author only manages to cover some [behaviours](http://www.erlang.org/doc/design_principles/des_princ.html), like servers (_gen_server_ to be specific), supervisors and applications. It's some excellent material however, so unless you're a seasoned Erlang developer this part alone could be reason enough to read this book. Sure, it would have been great to go into some more detail or cover more of OTP, but I think it's more than enough to make people understand the core principles of the framework so they are able to continue their studies with a solid foundation. I've seen many people struggle with the concept of processes and message passing concurrency, despite the relative simplicity of the [Actor model](http://www.wikiwand.com/en/Actor_model). But when Dave suggests thinking of processes somewhat like objects in the sense that they encapsulate state, sending messages suddenly will feel very familiar to OO programmers who find it hard to structure their code around Elixir/Erlang semantics.

Last but not least there's "More Advanced Elixir", which covers macros, protocols, the language's approach to mixins and a grab bag of topics that didn't quite warrant chapters of their own. I really liked this entire section, especially the parts on protocols and mixins. I feel like it gave me the tools to look at most Elixir code and figure out what's happening, which is quite an achievement for a book of this length.

### Closing thoughts

The world of Erlang/OTP is somewhat different from other programming languages, so no matter what your background is, chances are that certain things may feel somewhat alien to you. This is were "Programming Elixir" shines: it doesn't just try to get you to write Elixir, it wants you to write good idiomatic code because you understand what makes the language tick. The writing has a good flow and is to the point. On top of that each chapter comes with some well thought-out exercises that should keep motivated readers busy for a while. It's hard to imagine a better introduction to the language than "Programming Elixir", highly recommended!
