---
layout: post
title: "Goodbye Wordpress"
date: 2011-09-26 21:00
comments: true
categories:
  - blog
  - technology
  - octopress
---
After using [Wordpress](http://wordpress.org/) as my blogging engine
of choice for over a year (after moving away from [S9Y](http://s9y.org/)), I decided to switch this site to
[Octopress](http://octopress.org/). Why? Because it's cool!
No, seriously. I was actually quite happy with WP, it just always felt
like way more than I actually needed. OP on the other hand is super
lightweight, ritten in Ruby and uses some of my favorite technologies,
like Sinatra and Markdown. It doesn't stop there though: having the site rendered to static
files means I can get rid of MySQL and PHP on my little VPS, which
definitely is a plus. And it's also very cool to have all your posts
in a directory on your local file system, so you can use all your
favorite command line tools to search through or batch modify
them. Last but not least I'm now writing my posts in Emacs instead of
some crappy web input form (ok, granted, I could have done that before
too).

Now to the actual migration. I started by exporting my WP blog to
XML. This can be done in the administration backend under "Tools -
Export". Once I had this XML, I used the following script to convert
the posts to individual files for Octopress:

{% gist 1240781 %}

This was a fork of
[another script](https://gist.github.com/1240781/ac8a139bf73232c4333f0cc5734151cf8e5d1f87),
but tailored for my specific needs (e.g. all tags and categories of a
post were converted to categories and I exported the files to Textile
instead of Markdown because I used the former in my WP install through
a plugin).

Since some of my posts are quite popular and get linked to a lot, I
didn't want to loose my old links. I therefore wrote another script
that created 301 redirects for all of them:

{% gist 1241762 %}

The script can generate the necessary directives for Apache or Nginx,
so all you have to do is copy them into your web server
configuration. I also added another redirect rule for the RSS feed to
not annoy my subscribers:

```
RewriteRule ^/feed /atom.xml [R=301,L]
```

Of course I also didn't want to loose all my comments, so I had to
come up with a solution for that. I first
installed the WP [Disqus](http://disqus.com) plugin and exported all
my current comments. I then used some good old Emacs magic to convert
the list of redirects I had already generated to a CSV for Disqus'
[URL map migration tool](http://docs.disqus.com/help/39/). So far this
has not migrated all comments to the correct location, but it should
eventually work out (worst case I'll have to email Disqus support).

Last but not least I used Octopress' excellent
[customization suport](http://octopress.org/docs/theme/template/index.html)
to re-add some stuff to my headers, like the OpenID delegate.

Of course there are still some things that need to be done, some of them more urgently
then others. First off this site needs a better stylesheet. The
default is actually quite ok, but I want something with more
personality. I'll also have to go through my old posts and clean them
up a bit, the automatic conversion did some funny things at
times. I also want to re-integrate Flattr and PostRank, but I'm not
exactly in a hurry when it comes to that.

I hope this post was useful to some of you, I'm definitely happy with
my new blogging setup.

