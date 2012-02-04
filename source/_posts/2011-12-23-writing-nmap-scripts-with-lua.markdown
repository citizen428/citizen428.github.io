---
layout: post
title: "Writing nmap scripts with Lua"
date: 2011-12-23 13:54
comments: true
categories:
    - programming
    - lua
    - security
---
About 2 weeks ago I finally started looking into [Lua](http://www.lua.org/), a language that's been on my radar for a while but that I never really got around to play with so far. Then I realized that the [Nmap Scripting Engine](http://nmap.org/book/nse.html) uses Lua scripts, so I had an excuse to hack on this stuff for work. Here's my first small `nmap` script, which will extract the contents of the generator meta tag if there is one. It's nothing funky, but it shows how easy it is to write custom scripts for `nmap` thanks to the provided libraries like `http` and `shortport`.

{% codeblock lang:lua %}
description = [[
Displays the contents of the "generator" meta tag if there is one.
]]

author = "Michael Kohl"
license = "Same as Nmap--See http://nmap.org/book/man-legal.html"
categories = {"discovery", "safe"}

-- documentation skipped

require('http')
require('shortport')

portrule = shortport.http

action = function(host, port)
   local response, loc, generator

   response = http.get(host, port, '/')

   -- deal with redirects
   while response['status-line']:lower():match("^http/1.1 30[12]") do
      loc = response.header['location']
      response = http.get_url(loc)
   end

   for line in response.body:gmatch("[^\r\n]+") do
      generator = line:match('<meta name="generator" content="(.*)" />')
      if generator then
         return generator
      end
   end
end
{% endcodeblock %}

**Update:** Here's the version that's now part of `nmap`, it even made it to the default category.

{% codeblock lang:lua %}
description = [[
Displays the contents of the "generator" meta tag if there is one.
]]

author = "Michael Kohl"
license = "Same as Nmap--See http://nmap.org/book/man-legal.html"
categories = {"default", "discovery", "safe"}

-- documentation skipped

require('http')
require('shortport')
require('stdnse')

-- helper function
local follow_redirects = function(host, port, path, n)
   local pattern = "^[hH][tT][tT][pP]/1.[01] 30[12]"
   local response = http.get(host, port, path)

   while response['status-line']:match(pattern) and n > 0 do
      n = n - 1
      loc = response.header['location']
      response = http.get_url(loc)
   end

   return response
end

portrule = shortport.http

action = function(host, port)
   local response, loc, generator
   local path = stdnse.get_script_args('http-generator.path') or '/'
   local redirects = tonumber(stdnse.get_script_args('http-generator.redirects')) or 3

   -- Worst case: <meta name=Generator content="Microsoft Word 11">
   local pattern = '<meta name="?generator"? content="([^\"]*)" ?/?>'

   -- make pattern case-insensitive
   pattern = pattern:gsub("%a", function (c)
               return string.format("[%s%s]", string.lower(c),
                                              string.upper(c))
             end)

   response = follow_redirects(host, port, path, redirects)
   return response.body:match(pattern)

end
{% endcodeblock %}
