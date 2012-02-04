---
layout: post
title: "A Chrome Extension for Soup.io"
date: 2011-10-21 08:53
comments: true
categories:
  - programming
---
On October 29th the local [Google Technology User Group](http://www.gtug.at/) is hosting a Chrome Hackathon, in which I'll participate. As a little warm up exercise I created a small extension for my favorite microblogging service [Soup.io](http://soup.io), which basically just wraps their bookmarklet.

All in all this was a very nice experience, since the [developer's guide](http://code.google.com/chrome/extensions/devguide.html) is thorough and well written. After reading the ["Getting Started" tutorial](http://code.google.com/chrome/extensions/getstarted.html), almost all my questions were already answered. The others — which were generally caused by my poor JavaScript skills — could easily be solved by consulting Google and StackOverflow.

<!-- more -->

The heart of every Chrome extension is a file called `manifest.json`, which in the case of the Soup.io extension looks like this:

{% codeblock lang:javascript manifest.json %}
{
  "name": "Soup.io",
  "version": "0.3",
  "description": "For now this just wraps the Soup.io bookmarklet in a Chrome extension.",
  "browser_action": {
    "default_icon": "icon.png",
    "default_title": "Share on Soup.io"  },
  "content_scripts": [
    {
       "matches": ["http://*/*", "https://*/*"],
       "js": ["keyboard.js"],
       "run_at": "document_start"
    }
  ],
  "icons": { "16": "icon16.png",
             "48": "icon48.png",
             "128": "icon128.png" },
  "background_page": "background.html",
  "permissions": [
    "tabs", "http://*/*", "https://*/*/"
  ]
}
{% endcodeblock %}

Here we define some general information, like the extension's name, version and description, as well as [icons in various sizes](http://code.google.com/chrome/extensions/manifest.html#icons) and needed [permissions](http://code.google.com/chrome/extensions/manifest.html#permissions). The `browser_action` section specifies the icon — including mouseover text — that will be displayed next to Chrome's address bar, whereas the `background_page` is a script that runs in the extension process.

The core of my background page is a simple JavaScript function, which calls the bookmarklet provided by Soup.io (which I [URL decoded](http://meyerweb.com/eric/tools/dencoder/) and formatted nicely):

{% codeblock lang:javascript background.html (1/2) %}
chrome.browserAction.onClicked.addListener(function(tab) {
  chrome.tabs.executeScript(null, {file:"popup.js"});
});
{% endcodeblock %}

Easy so far, and I got to a first working version in about half an hour. I then wanted to add some keyboard shortcuts, which was slightly more involved, since Chrome doesn't provide an API for that. Instead you use JavaScript listeners and [message passing](http://code.google.com/chrome/extensions/messaging.html) to implement this. If you look back at the manifest, you'll see a section called `content_script`, which specifies a file that runs in the context of the web page you are looking at. Here I set up a listener for `keyup` events.

{% codeblock lang:javascript keyboard.js %}
function keyListener(e) {
    if (e.altKey && e.shiftKey && e.keyCode == 83) {
        chrome.extension.sendRequest({name: "openPopUp"});
    }
};

window.addEventListener("keyup", keyListener, false);
{% endcodeblock %}

When the user presses the right combination (Alt-Shift-S in this case), a request named `openPopUp` gets sent, which we deal with in another listener in `background.html`:

{% codeblock lang:javascript background.html (2/2) %}
chrome.extension.onRequest.addListener(
   function(request, sender, sendResponse) {
       if (request.name == 'openPopUp') {
           chrome.tabs.executeScript(null, {file:"popup.js"});
       }
   });
{% endcodeblock %}

And that's it, a simple Chrome extension, including a keyboard shortcut. Hosting the extension is a piece of cacke if you use the developer dashboard of the Chrome [web store](https://chrome.google.com/webstore), but there's a one time registration fee of USD 5 for it. Here's the current version:

[Soup.io for Google Chrome](https://chrome.google.com/webstore/detail/jhfapglpemknnoacmdnhikhnpjkgpmcc)

This was a fun experience, I'm definitely looking forward to the hackathon!









