---
layout: post
title: Smalltalk's Method Finder in Ruby
date: 2011-2-9
comments: true
categories:
- programming
- ruby
---
<em>Update: The code is on Github now - <a href="https://github.com/citizen428/MethodFinder">https://github.com/citizen428/MethodFinder</a>

I changed the argument order (so you don't have to always specify empty arrays), added support for blocks, do some method filtering based on arity and ensure that the streams get properly restored. I left the original code in this post for reference though.
</em>

I always was quite fond of Squeak's Method Finder, which let's you enter a receiver, arguments and the desired result and will show you all methods that will return this result when sent to the receiver with the provided arguments. Since I started mentoring courses at RubyLearning 2 years ago I often thought that something like this might be nice for the students of our core course.

Last night I was about to start looking for an implementation of Method Finder in Ruby I found some years ago, but then decided to just write my own, which is more fun and doesn't take long. Here we go:

{% codeblock lang:ruby %}
class MethodFinder
  def self.find(obj, args, res)
    redirect_streams

    found = obj.methods.select do |m|
      o = obj.is_a?(Numeric) ? obj : obj.dup
      (o.send(m, *args) rescue nil) == res
    end

    restore_streams
    found
  end

  def self.redirect_streams
    @orig_stdout = $stdout
    @orig_stderr = $stderr
    $stdout = File.new('/dev/null', 'w')
    $stderr = File.new('/dev/null', 'w')
  end

  def self.restore_streams
    $stdout = @orig_stdout
    $stderr = @orig_stderr
  end

  private_class_method :redirect_streams, :restore_streams
end
{% endcodeblock %}

Here's how you use it:

{% codeblock lang:ruby %}
>> MethodFinder.find(16,5,[3,1])
=> ["divmod"]
>> MethodFinder.find("abc",[/./],%w(a b c))
=> ["scan"]
>> MethodFinder.find("aBc",[],"AbC")
=> ["swapcase!", "swapcase"]
>> MethodFinder.find(10,2,100)
=> ["**"]
>> MethodFinder.find([1,nil,3,4,nil,6],[],[1,3,4,6])
=> ["compact", "compact!"]
{% endcodeblock %}

I think I'll pack this up as a gem for our course participants to install and use in their IRBs, but before that I might add/change a couple of things:

<ul>
	<li>Exclude method's based on arity to reduce the number of message sends. <em>Done.</em></li>
	<li>Add support for blocks which is trivial. <em>Done.</em></li>
	<li>Maybe switch the order of arguments and expected result, so you don't always have to specify an empty array for the arguments. I am a bit torn on this one though, since I like having the argument list explicit. <em>Done.</em></li>
	<li>Redirect <code>stdout</code> and <code>stderr</code> to a StringIO object instead of <code>/dev/null</code> unless someone tells me that the latter works on Windows. <em>Done.</em></li>
	<li>Maybe add some sort of method blacklist so they won't get tried in the block. <em>Done.</em></i>
</ul>

This shouldn't take long, but still may have to wait for a couple of weeks because of a trip I'm taking.
