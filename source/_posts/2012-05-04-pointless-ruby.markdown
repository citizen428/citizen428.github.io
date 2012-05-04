---
layout: post
title: "Pointless Ruby"
date: 2012-05-04 13:03
comments: true
categories:
    - ruby
---
It's no secret that I'm rather fond of [functional programming](http://en.wikipedia.org/wiki/Functional_programming) and also try to apply it to my Ruby coding, at least as long as it doesn't become too unidiomatic.

Being at home sick I got bored, so I played around a bit in IRB to create a composition function that will allow me to create higher-order functions in a [pointfree style](http://www.haskell.org/haskellwiki/Pointfree) (sometimes also referred to as "pointless style", hence the title of this post). I know that [Facets](http://rubyworks.github.com/facets/) has `Proc#compose` and `Proc#*`, but they only work with `Proc` objects. Ruby being the flexible beast that it is, it took me about two minutes to come up with a working solution, which I called `fn` because it basically returns a function and `compose` was too long for my taste:

{% codeblock lang:ruby %}
def fn(*funs)
  -> x { funs.inject(x) { |v, f| v.send(f) } }
end
{% endcodeblock %}

Here it is in action:

{% codeblock lang:ruby %}
["foo bar", "baz qux"].map &fn(:split, :last)
#=> ["bar", "qux"]
{% endcodeblock %}

I know that this isn't really shorter than `{ |x| x.split.last }`, but usefullness isn't really the goal of a pointless exercise ;-)

So far so good, but if one doesn't really care about pointfree style, wouldn't it also be fun to mix and match procs and symbols? It sure would:

{% codeblock lang:ruby %}
def fn(*funs)
  -> x do
    funs.inject(x) do |v, f|
      Proc === f ? f.call(v) : v.send(f)
    end
  end
end
{% endcodeblock %}

Let's see this in action:

{% codeblock lang:ruby %}
(1..3).map &fn(:next, -> x { x * x }, -> x { x.to_f / 2 } )
#=> [2.0, 4.5, 8.0]
{% endcodeblock %}

While coding along, I thought that I might as well tackle something that always rather annoyed me, being unable to use `Symbol#to_proc` when you actually get arrays from the `Enumerator`:

{% codeblock lang:ruby %}
(1..10).each_cons(2).map(&:+)
#=> ArgumentError: wrong number of arguments(0 for 1)
  ...
{% endcodeblock %}

Let's remedy that with yet another version of `fn`:

{% codeblock lang:ruby %}
def fn(*funs)
  -> x do
    funs.inject(x) do |v, f|
      Enumerable === v ? v.first.send(f, *v[1..-1]) : v.send(f)
    end
  end
end
{% endcodeblock %}

{% codeblock lang:ruby %}
(1..6).each_cons(2).map &fn(:+)
#=> [3, 5, 7, 9, 11]
(1..6).each_cons(2).map &fn(:+, :next)
#=> [4, 6, 8, 10, 12]
{% endcodeblock %}

Here's the final version that combines everything, the code is not very pretty though:

{% codeblock lang:ruby %}
def fn(*funs)
  -> x do
    funs.inject(x) do |v, f|
      if Proc === f
        f.call(*v)
      else
        if Enumerable === v
          h, *t = v
          h.send(f, *t)
        else
          v.send(f)
        end
      end
    end
  end
end
{% endcodeblock %}

Tara:

{% codeblock lang:ruby %}
(1..6).each_cons(2).map &fn(:+, :next, -> x { x.to_s(2) } )
#=> ["100", "110", "1000", "1010", "1100"]
{% endcodeblock %}

Note: I'm sick, all of this is untested, does not take into account corner cases of any kind and may harm your pet. It was however a fun little diversion that showed once again how flexible Ruby is.
