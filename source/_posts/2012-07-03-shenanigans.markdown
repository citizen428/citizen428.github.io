---
layout: post
title: "Shenanigans"
date: 2012-07-03 19:46
comments: true
categories:
    - ruby
    - programming
---
About 1.5 month ago I finally packaged some of my Ruby extension methods into the aptly named [Shenanigans](https://rubygems.org/gems/shenanigans) gem. It's similar to [Facets](http://rubyworks.github.com/facets/) or [ActiveSupport](http://as.rubyonrails.org/), but probably less useful to most people. Also some of the methods actually are in Facets, although sometimes with different names or slightly different semantics. Anyway, since some people told me they actually do like the gem, here's a quick summary of what the different methods do and how they can be useful.

* [Array#^](http://rubydoc.info/gems/shenanigans/1.0.4/Array:%5E): Ruby defines [Set#^](http://www.ruby-doc.org/stdlib-1.9.3/libdoc/set/rdoc/Set.html#method-i-5E), which returns the elements exclusive between the set and a given enum. Since a lot of people use arrays instead of proper sets in Ruby, I found it makes a nice addition to [Array#|](http://www.ruby-doc.org/core-1.9.3/Array.html#method-i-7C) and [Array#&](http://www.ruby-doc.org/core-1.9.3/Array.html#method-i-26).

* [Array#random_subarray](http://rubydoc.info/gems/shenanigans/1.0.4/Array:random_subarray): Generates one or more random subarrays of an array, using the fact that Ruby can index integers to get the bit values to ensure fast uniform distributions. Similar to [Array#sample](http://www.ruby-doc.org/core-1.9.3/Array.html#method-i-sample), but can generate several subarrays at once.

* [Array#zip_with](http://rubydoc.info/gems/shenanigans/1.0.4/Array:zip_with): The more general form of `zip` (e.g. in Haskell you could define `zip = zipWith (,)`). Like Ruby's [Enumerable#inject](http://ruby-doc.org/core-1.9.3/Enumerable.html#method-i-inject) it can take a symbol argument or a block, and like Haskell's `zip` it discards excess array elements if one list is shorter than the other, whereas Ruby's [Array#zip](http://www.ruby-doc.org/core-1.9.3/Array.html#method-i-zip) only does that if the receiver is shorter than the argument (it adds `nil`s in the other case).

* [Hash#has_shape?](http://rubydoc.info/gems/shenanigans/1.0.4/Hash:has_shape%3F): A quick way to check if a hash's keys are of certain classes. This does feel a bit strange in a duck-typed language like Ruby, but someone asked for it on StackOverflow and it was easy enough to write. This could be useful in validations or unit tests though.

* [Hash#to_ostruct](http://rubydoc.info/gems/shenanigans/1.0.4/Hash:to_ostruct): I really like the [rails_config](https://github.com/railsjedi/rails_config) gem. I therefore decided to write something that gives me similar functionality (although a lot simpler) for plain Ruby projects. Basically this recursively converts a hash and all nested hashes into [OpenStruct](http://ruby-doc.org/stdlib-1.9.3/libdoc/ostruct/rdoc/OpenStruct.html) instances. Populate the hash with Ruby's [YAML](http://corelib.rubyonrails.org/classes/YAML.html) support and you have an instant settings object.

* [Kernel#fn](http://rubydoc.info/gems/shenanigans/1.0.4/Kernel:fn): I admit, this was more of a "because I can" method. Originally I wrote this so I could compose blocks in [pointfree style](http://www.haskell.org/haskellwiki/Pointfree), but later added support for [Proc](http://www.ruby-doc.org/core-1.9.3/Proc.html) instances too.

* [Kernel#prompt](http://rubydoc.info/gems/shenanigans/1.0.4/Kernel:prompt): While it's great that Ruby's [IO#gets](http://www.ruby-doc.org/core-1.9.3/IO.html#method-i-gets) is so general, I always wanted something like Python's [raw_input](http://docs.python.org/library/functions.html#raw_input) for command line apps. Additionally you can also automatically call any of the numeric conversions, and I'm pondering adding support for every unary string method.

* [Kernel#with](http://rubydoc.info/gems/shenanigans/1.0.4/Kernel:with): I think [Object#tap](http://ruby-doc.org/core-1.9.3/Object.html#method-i-tap) is great. However, I sometimes see it used as a replacement for [Enumerable#inject](http://ruby-doc.org/core-1.9.3/Enumerable.html#method-i-inject) or [Enumerable#each\_with\_object](http://ruby-doc.org/core-1.9.3/Enumerable.html#method-i-each_with_object), but for some reason I never really liked the semantics of it. And since I'm anal about that sort of thing I added this Pascal/ActionScript like `with` statement.

* [Object#display](http://rubydoc.info/gems/shenanigans/1.0.4/Object:display): Having no proper object-oriented way to print objects in Ruby always kinda bothered me, so I abused [Object#tap](http://ruby-doc.org/core-1.9.3/Object.html#method-i-tap) as a wrapper around [Kernel#puts](http://www.ruby-doc.org/core-1.9.3/Kernel.html#method-i-puts) and [Kernel#print](http://www.ruby-doc.org/core-1.9.3/Kernel.html#method-i-print). Additionally this is aliased to the name `d`, which I stole from [irbtools](https://github.com/janlelis/irbtools). The latter form makes it great for debugging method chains and the likes.

* [Object#it](http://rubydoc.info/gems/shenanigans/1.0.4/Object:it): I always disliked blocks of the form `{ |x| x }` that sometimes crop up when using  [Enumerable#group_by](http://ruby-doc.org/core-1.9.3/Enumerable.html#method-i-group_by) or similar methods. Since Ruby lacks an identity method and `id` has historically been taken, I decided to name it `it`.
