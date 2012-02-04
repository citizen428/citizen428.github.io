---
layout: post
title: "Ruby GitHub projects Atom feed"
date: 2011-11-23 20:43
comments: true
categories:
    - ruby
    - programming
---
The other day I found an [interesting post](http://xach.livejournal.com/298220.html) by Lisper extraordinaire [Zach Beane](http://xach.livejournal.com/) on [Planet Lisp](http://planet.lisp.org/), where he describes his program to generate a feed for the [GitHub page about recently created Common Lisp repos](https://github.com/languages/Common%20Lisp/created).

Since good artists borrow and great artists steal, I decided to do the latter with his idea and implemented something similar for the [new Ruby repos](https://github.com/languages/Ruby/created). It uses [open-uri](http://www.ruby-doc.org/stdlib-1.9.3/libdoc/open-uri/rdoc/) for fetching the page, [Nokogiri](http://nokogiri.org/) for parsing it and [Builder](http://builder.rubyforge.org/) for generating the Atom feed.

``` ruby
require 'open-uri'
require 'nokogiri'
require 'builder'

html = open("https://github.com/languages/Ruby/created")
doc = Nokogiri::HTML.parse(html)

atom = Builder::XmlMarkup.new(:target => STDOUT, :indent => 2)
atom.instruct!
atom.feed "xmlns" => "http://www.w3.org/2005/Atom" do
  atom.id "urn:citizen428:github:newrepos"
  atom.updated Time.now.utc.iso8601(0)
  atom.title "New GitHub Ruby Repos", :type => "text"
  atom.link :rel => "self", :href => "/ruby_github.atom"
  doc.xpath("//table[@class='repo']/tr/td[@class='title']/a").each do |title|
    name = title.content
    owner = title.at_xpath("../../td[@class='owner']/a").content
    desc = title.at_xpath("../../following-sibling::tr/td[@class='desc']").content
    date = Time.parse(title.at_xpath("../../td[@class='date']").content)
    atom.entry do
      atom.title "#{owner}: #{name}"
      atom.author { atom.name owner }
      atom.link "href" => "https://github.com#{title.attributes["href"].value}"
      atom.id "urn:citizen428:github:#{owner}:#{name}"
      atom.published date.utc.iso8601(0)
      atom.updated date.utc.iso8601(0)
      atom.content desc, :type => "html"
    end
  end
end
```

I'm hosting [the feed](http://citizen428.net/ruby_github.atom) on this blog (updated regularly via cron), add it to your feed reader to discover potentially interesting new Ruby projects.
