---
layout: post
title: "A little Pow helper"
date: 2011-11-22 21:08
comments: true
categories:
    - programming
    - ruby
    - osx
---
If you are a Ruby developer on MacOS X, you probably love [Pow](http://pow.cx) as much as I do. While skimming the [fine manual](http://pow.cx/manual.html) today, I noticed [section 3.2](http://pow.cx/manual.html#section_3.2), "Reading the Current Configuration", which explains that you can query Pow's config and status via simple HTTP requests:

``` bash Query Pow's status http://pow.cx/manual.html#section_3.2 Pow manual
curl -H host:pow localhost/status.json
```

Since I know I'd probably forget that sooner than later, I immediately wrapped it up in a small script, which I called `pow-show` and put in `~/bin`:

``` ruby ~/bin/pow-show
#!/usr/bin/env ruby

require 'json'

unless %w(status config).include?(ARGV[0])
  puts "Usage: #{File.basename($0)} status|config"
  exit 1
end

jj JSON.parse(`curl -s -H host:pow localhost/#{ARGV[0]}.json`)
```

Here's some example output:

``` javascript pow-show status
{
  "pid": 802,
  "version": "0.3.2",
  "requestCount": 134
}
```

``` javascript pow-show config
{
  "bin": "/Users/name/Library/Application Support/Pow/Versions/0.3.2/bin/pow",
  "dstPort": 80,
  "httpPort": 20559,
  "dnsPort": 20560,
  "timeout": 900,
  "workers": 2,
  "domains": [
    "dev"
  ],
  "extDomains": [

  ],
  "hostRoot": "/Users/name/Library/Application Support/Pow/Hosts",
  "logRoot": "/Users/name/Library/Logs/Pow",
  "rvmPath": "/Users/name/.rvm/scripts/rvm"
}
```

Maybe that's useful for someone else too.
