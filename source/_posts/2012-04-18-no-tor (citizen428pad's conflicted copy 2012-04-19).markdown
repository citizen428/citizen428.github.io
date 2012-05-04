---
layout: post
title: "No Tor, no more customer"
date: 2012-04-18 00:10
comments: true
categories:
    - tor
    - freedom
---
*Update:* Server4You rebooted the machine in recovery mode, I removed the Tor packages and the system is back online, which means at least the [mirror](http://tor.blingblingsquad.net/) is back online.

I've been running a [Tor](https://www.torproject.org/) relay since sometime in early 2006. There's many good reasons for doing so, but what motivated me personally was my experience of studying in China for one year and having to live with a censored Internet connection myself (theoretically, as there was an [easy way around it](http://embraceubuntu.com/2006/12/08/ssh-tunnel-socks-proxy-forwarding-secure-browsing/) if you had a server outside of China to SSH to). Alas our system hasn't been an exit node in a while, but it's still better than nothing.

Today I was contacted by my [hosting provider](http://server4you.de)'s abuse team, telling me that my server has been shut down because of a ToS violation. I [immediately was afraid](https://twitter.com/#!/citizen428/status/192189329177718784) that they might complain about the Tor relay, but still had hope that I might be wrong. Ten hours later, after I got no reply to about five support requests, I decided to call their hotline. The employee first told me that he has no way to see why the abuse team blocked my server, but then said that running a Tor node may have been the reason. What ensued was a five minute conversation full of WTFs, facepalms and headdesks on my part, but the bottom line is that Server4You is of the opinion that it is illegal to run a Tor relay in Germany because of the ["Hackerparagraf"](http://de.wikipedia.org/wiki/Hackerparagraf). If you don't know what that is, [read up](http://hackingexpose.blogspot.com/2012/04/watch-out-white-hats-european-union_3977.html) on recent pushes to enforce similar legislation EU wide.

After a fruitless discussion of said law, the — admittely nice — guy on the phone reassured me that my server will be restarted if I agree to disable the Tor node. I politely told him that I have no interest in using their services at all if I can't use them the way I want to, so I'll cancel my contract with Server4You as soon as possible. For the time being I donated some money to Tor, moved my blog over to [Heroku](http://www.heroku.com/) (yay [Octopress](http://octopress.org/)) and started evaluating hosting providers in countries with saner legislation (as long as there still are some).

Sure, there's probably a lot of really bad things happening over the Tor network, but those shouldn't be used to discredit the tool's legitimate uses. If we start blaming technology instead of people, we might as well turn off the Internet.
