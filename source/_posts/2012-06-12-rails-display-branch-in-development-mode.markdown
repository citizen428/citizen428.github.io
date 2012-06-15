---
layout: post
title: "Rails — Display branch in development mode"
date: 2012-06-12 21:33
comments: true
categories:
    - programming
    - ruby
---
At work we are building a Rails app that's supposed to run on appliances instead of being hosted by us. To make it easier to deal with bug reports and feature requests, we always display the version number in the footer. A couple of days ago I thought it'd be handy to replace this with the current branch in development mode. Using [Grit](https://github.com/mojombo/grit) the solution couldn't have been any simpler:

{% codeblock app/helpers/application_helper.rb lang:ruby %}
def version_div
  version = Rails.env.development? ? ::Grit::Repo.new(Rails.root).head.name : ::OurApp::Application::Version
  content_tag(:div, content_tag(:span, version), :class => "version")
end
{% endcodeblock %}

Call `version_div` in a view and you are good to go. If dragging in another dev dependency for just one call bothers you, you can also shell out to get the current branch: `git rev-parse --abbrev-ref HEAD`.
