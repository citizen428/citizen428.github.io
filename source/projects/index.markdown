---
layout: page
title: "Projects"
date: 2012-08-09 20:37
comments: true
sharing: true
footer: true
---
This is a list of some of my (personal) projects. Since I contribute quite regularly to FOSS, I can't list every project I ever committed a patch for, but this is a good selection.

## Ruby:

* [RubyLearning.org](http://rubylearning.org): I started being a mentor for RubyLearning in early 2009. Besides teaching and improving the Ruby courses, I also helped to create, organize and teach the "Clojure for beginners" and "Clojure 101" courses.

* [MethodFinder](http://citizen428.github.com/methodfinder/): A port of [Squeak](http://www.squeak.org/)'s Method Finder to Ruby. Mentioned on [Ruby5](http://ruby5.envylabs.com/episodes/165-episode-162-march-29-2011/stories/1451-find-that-method-you-ve-been-looking-for-with-ruby-method-finder) and [RailsCasts](http://railscasts.com/episodes/48-console-tricks-revised) (view on [Rubygems](https://rubygems.org/gems/methodfinder)).

* [Shenanigans](http://citizen428.github.com/shenanigans/): My personal Ruby extension library (view on [Rubygems](https://rubygems.org/gems/shenanigans)).

* [tryit](https://github.com/sevenmaxis/tryit): After a discussion on StackOverflow, Sergey Gopkalo and I created this gem as an alternative to Rails' `Object#try` method. Personally I prefer it syntax-wise and it's also useful in pure Ruby projects (view on [Rubygems](https://rubygems.org/gems/tryit)).

* [Revolver](http://citizen428.github.com/revolver/): A fixed-size LIFO data structure (view on [Rubygems](https://rubygems.org/gems/revolver)).

* [JRuby](https://github.com/jruby/jruby): I sometimes use JRuby and while doing so [fixed 2 bugs in it](https://github.com/jruby/jruby/commits?author=citizen428).

* [unsavory](http://citizen428.github.com/unsavory): Remove outdated links from [Pinboard](http://pinboard.in/). Originally written for Delicious, hence the name (view on [Rubygems](https://rubygems.org/gems/unsavory)).

* [kraken-build](https://rubygems.org/gems/kraken-build): A simple tool that automatically adds/removes Git branches as Jenkins jobs. Originally written by Andreas Tiefenthaler with some improvements and refactorings by me (view on [Rubygems](https://rubygems.org/gems/kraken-build)).

* [koelner_phonetic_encoder](https://rubygems.org/gems/koelner_phonetic_encoder): An implementation of the [Kölner Phonetik](http://de.wikipedia.org/wiki/K%C3%B6lner_Phonetik) algorithm which I wrote together with Anton Bangratz while working for [Tupalo](http://tupalo.com). Neither of us is still working on this.

* [tupalo_api_client](https://rubygems.org/gems/tupalo_api_client): A convenient wrapper for the [Tupalo](http://tupalo.com) API, written by Andreas Tiefenthaler and me. As far as I know this gem is currently unmaintained.

* [Emacs Rocks!](http://emacsrocks.com/): While the site is developed and run by Magnar Sveen, I added [Atom support](https://github.com/magnars/emacsrocks.com/pull/2) with Ruby's excellent [Builder](http://builder.rubyforge.org/) library.

## Chrome extensions:

* [Soup](https://chrome.google.com/webstore/detail/jhfapglpemknnoacmdnhikhnpjkgpmcc): Since I don't use the bookmarks bar, I converted [Soup.io](http://soup.io)'s bookmarklet into a Chrome extension.

* [Nosy](https://chrome.google.com/webstore/detail/iabnohdojoklefphbdnniglicbgioblk): Written for the October 2011 GTUG Vienna Chrome extension hackathon with Bernd Forstner and Ludwig Hammel. We placed 2nd in the voting.

* [Timely Everywhere](https://chrome.google.com/webstore/detail/kodcdlioojimglcijmipanmcingnojhe): Another port of a bookmarklet to a Chrome extension, this one for [Timely](http://timely.is/).

## Misc

* [nmap http-generator script](http://nmap.org/nsedoc/scripts/http-generator.html): Since I sometimes do security related work, I use `nmap` quite a bit. So when I decided to learn some Lua, I wrote this discovery tool that is now part of `nmap`'s default script set.

* [Happy Nerds](http://happynerds.net/): A collection of programming resources for children. Created by me and made pretty by [Thomas Fadrus](http://fadr.at).

* [ClojureX](https://github.com/citizen428/ClojureX): While Clojure was still young, it wasn't always easy to get a development environment up and running. ClojureX was a distribution that tried to simplify the process on OSX and was fairly popular for a while. I have not worked on this past Clojure 1.2 though, so the project is not very useful in its current form.

* [Gentoo Linux](http://gentoo.org): I was a volunteer developer for Gentoo Linux from 2004 to 2006, mostly working on Ruby libraries and related packages, as well as games and Gentoo/FreeBSD. I also was a regular contributor for the Gentoo Weekly News.
