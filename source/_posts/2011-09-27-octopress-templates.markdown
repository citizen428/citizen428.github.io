---
layout: post
title: "Octopress Templates"
date: 2011-09-27 16:58
comments: true
categories:
  - blog
  - octopress
  - programming
---
When deciding to switch from Wordpress to Octopress one of the main reasons was that I don't need most of the advanced features the former offers. However, for my weekly [Information Overload](http://citizen428.net/blog/categories/informationoverload/) series I relied on two features, namely templates and scheduling: I created a new post from the template and then filled it with content over the week. Once I was done I'd schedule it for publishing on Sunday afternoon. Here's how I plan to do this with Octopress.<!-- more -->
Templating
---

I created a new Rake task named "new_overload", that looks like this:

{% codeblock lang:ruby %}
desc "Create a new Information Overload template #{source_dir}/#{posts_dir}"
task :new_overload do
  raise "### You haven't set anything up yet. First run `rake install` to set up an Octopress theme." unless File.directory?(source_dir)
  require 'date'
  require './plugins/titlecase.rb'
  mkdir_p "#{source_dir}/#{posts_dir}"
  date = (Date.today + (7-Date.today.wday))
  title = "Information Overload #{date.strftime("%Y-%m-%d")}"
  filename = "#{source_dir}/#{posts_dir}/#{date.strftime('%Y-%m-%d')}-#{title.to_url}.#{new_post_ext}"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    system "mkdir -p #{source_dir}/#{posts_dir}/";
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/&/,'&amp;').titlecase}\""
    post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
    post.puts "comments: true"
    post.puts "categories: "
    post.puts "published: false"
    post.puts "---"
    post.puts "* []()\n" * 10
  end
end
{% endcodeblock %}

This is very similar to the standard "new_post" task, but generates
the post with the date of the current week's Sunday. It also marks it
as a draft by adding "published: false" to the YAML header and inserts
a template for an unordered list in Markdown syntax.

I'm not exactly sure how to handle the scheduling yet, but I think
good old [at](http://en.wikipedia.org/wiki/At_(Unix) will come in
handy here.
