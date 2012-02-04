---
layout: post
title: Emacs twittering-mode
date: 2011-1-21
comments: true
categories:
- emacs
---
When my [usual Twitter client](http://nambu.com/) stopped working last Tuesday, I got inspired by [Ruben's Emacs 30 Day Challenge](http://www.mostlymaths.net/2010/12/emacs-30-day-challenge.html) and decided to look what Emacs had to offer in terms of Twitter clients. On the always helpful EmacsWiki I found a [list of available options](http://emacswiki.org/emacs/Twitter) and after some browsing settled on [twittering-mode](http://twmode.sourceforge.net/). I got hooked really fast, so even after getting Nambu to work again (somehow authentication got screwed up), I decided to stay with Emacs for my Twitter needs.

Here's a screenshot from the Emacs wiki (although I usually have the avatars turned off):

<img src="http://www.emacswiki.org/pics/static/TwitteringModeScreenshot.jpg" alt="Emacs twittering-mode" title="TwitteringMode" />

If you also want to give this awesome mode a spin, I recommend starting at [this EmacsWiki page](http://www.emacswiki.org/emacs/TwitteringMode), which has installation instructions, a summary of key-bindings and options and some discussion by users.

Here are the relevant parts of my Emacs config, with extra comments added for this post:

{% codeblock lang:scheme %}
(require 'twittering-mode)
; update every 10 minutes
(setq twittering-timer-interval 600)
(setq twittering-url-show-status nil)
; Show character count in compose buffer
(setq twittering-use-show-minibuffer-length t)
; I added is.gd support myself, pull request sent.
; standard options are tinyurl and toly
(setq twittering-tinyurl-service 'is.gd)
; See http://www.reverttoconsole.com/blog/nix/twitter-mode-for-emacs-with-oauth/
(setq twittering-use-master-password t)

; This tells twittering-mode which time line buffers
; to open when starting
(setq twittering-initial-timeline-spec-string
      '(":friends"
        ":replies"
        ":direct_messages"
        ":search/clojure/"
        ":search/#jlang/"
        "citizen428/tupaleros"))

; some key bindings
(add-hook 'twittering-mode-hook
          (lambda ()
            (mapc (lambda (pair)
                    (let ((key (car pair))
                          (func (cdr pair)))
                      (define-key twittering-mode-map
                        (read-kbd-macro key) func)))
                  '(("R" . twittering-native-retweet)
                    ("l" . twittering-goto-next-thing)))))
{% endcodeblock %}

<strong>TImelines</strong>

As you can see `twittering-initial-timeline-spec-string` defines which timeline buffers get opened when starting. This is really convenient, since you can use `f` and `b` to cycle through them. If you want to see only the timeline of the author of the currently active tweet hit `v`, `C-c C-v` will open the profile in a web browser. `V` gives you access to all pre-defined timelines like `:public` or `:retweets_of_me`.

<strong>Tweets</strong>

Inside a timeline `j` and `k` let you jump from tweet to tweet, whereas `p` and `n` go to the previous or next tweet by the same author. Replying to a tweet is as easy as pressing enter. I've rebound `twittering-native-retweet` (for "new style" retweets) to `R` instead of the somewhat unwieldy `C-u C-c Enter`, "organic" retweets are possible with `C-c Enter`. `r` opens related tweets, so it's easy to follow conversations and `u` let's you post a new tweet.  My favorite function is probably `twittering-goto-next-thing` (bound to `l` in my setup), which  jumps to the next username (to make it easy to visit the profile), URI, or timeline symbol (like a hashtag).

There are many more useful functions and key combinations (like `i` for showing/hiding avatars), it's best to look them up on the EmacsWiki page. I'm very impressed by and happy with my new Twitter client, browsing through my update stream has never been so fast and efficient.
