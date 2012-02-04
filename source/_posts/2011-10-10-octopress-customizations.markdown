---
layout: post
title: "Octopress Customizations"
date: 2011-10-10 19:59
comments: true
categories:
  - blog
  - octopress
---
I finally found some time to give this blog a less generic look, which was really easy thanks to [Octopress's great theme and customization support](http://octopress.org/docs/theme/).

First I changed some colors in `sass/custom/_colors.scss`:

{% codeblock lang:sass %}
$header-bg: #000;
$subtitle-color: lighten($header-bg, 58);
$nav-bg: desaturate(lighten(#FF7100, 18), 5)
$link-color: #FF7100;
$footer-bg: #CCC;
{% endcodeblock %}

Then I reduced the width of the body, the size of the dates and article titles as well as the codeblocks (all of this can be done in `sass/custom/_styles.scss`):

{% codeblock lang:sass %}
body {
  max-width: 1100px;
}
time {
  font-size: 14px;
}
h1 {
  font-size: 2.2em;
}
section {
  > h1 {
    font-size: 1.5em;
  }
}
codeblock {
    font-size: 1.1em;
}
{% endcodeblock %}

Last but not least I found some [excellent free social media icons](http://www.elegantthemes.com/blog/resources/free-social-media-icon-set) for the sidebar.

I hope you like the changes, I do and I was also pleasantly surprised how easy it is to change the look of an Octopress blog.

