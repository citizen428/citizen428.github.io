---
layout: post
title: "Ruby — Left section for infix operators"
date: 2012-07-30 23:24
comments: true
categories:
    - programming
    - ruby
---
Let me admit right away that this is yet another "because I can" type of post with little practical use. With that out of the way, today's topic is [sections of infix operators](http://www.haskell.org/haskellwiki/Section_of_an_infix_operator) as known from e.g. Haskell. Here's an example:

{% codeblock lang:haskell %}
λ> map (2+) [1..3]
[3,4,5]
{% endcodeblock %}

As you can see the function to be `map`ped is specified with the [partially applied](http://www.haskell.org/haskellwiki/Partial_application) function `(2+)` (or `(+2)` for that matter).

Here's how we would have to write the same code in Ruby:

{% codeblock lang:ruby %}
(1..3).map { |i| i + 2 }
{% endcodeblock %}

This could also be written in a [pointfree](http://www.haskell.org/haskellwiki/Pointfree) way, but alas that would make the code rather hard to understand for less experienced Rubyists:

{% codeblock lang:ruby %}
(1..3).map(&2.method(:+))
{% endcodeblock %}

But because Ruby is as flexible as it is, adding partial application is actually really trivial:

{% codeblock lang:ruby %}
class Fixnum
  alias :add :+

  def +(o=nil)
    o ? self.add(o) : lambda { |x| self + x }
  end
end
{% endcodeblock %}

And now we can do this:

{% codeblock lang:ruby %}
(1..3).map(&2.+) #=> [3, 4, 5]
{% endcodeblock %}

Not too bad I think and rather consistent with Ruby's overall style. Alas I don't think we are likely to see partial application on a language level any time soon though...
