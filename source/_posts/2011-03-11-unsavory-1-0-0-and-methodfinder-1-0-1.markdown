---
layout: post
title: unsavory 1.0.0 and methodfinder 1.0.1
date: 2011-3-11
comments: true
categories:
- programming
- ruby
---
[unsavory](https://github.com/citizen428/unsavory) has reached maturity (meaning I can't really come up with any more stuff I want to add), so i decided it's enough with the conservative versioning and jumped from 0.3.3 to 1.0.0 :-) Right before Christmas I had changed it from Delicious to [Pinboard](http://pinboard.in) and today I replaced the previous - rather verbose - output with a nice little [progressbar](http://0xcc.net/ruby-progressbar/index.html.en) and a log file.

I also did a mini update to [methodfinder](https://github.com/citizen428/methodfinder), which now uses [StringIO](http://www.ruby-doc.org/stdlib/libdoc/stringio/rdoc/index.html) objects instead of `/dev/null` for output redirection, which should make the Windows users out there happy.
