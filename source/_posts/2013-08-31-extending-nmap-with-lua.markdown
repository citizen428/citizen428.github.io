---
layout: post
title: "Extending Nmap with Lua"
date: 2013-08-31 17:49
comments: true
categories:
    - programming
---
_I originally wrote this piece for a pentesting magazine, but after some unprofessional behavior by their editor I lost my cool and decided not too publish it there. It'd be a shame to let it go to waste though, so I after almost three month I finally decided to put it up on my blog._

Whether you are working as a security professional or a network administrator, chances are that [Nmap](http://nmap.org/) (“Network Mapper”) is part of your regular toolkit. For many people this project, which was started by Gordon "Fyodor" Lyon in 1997, is the first choice when it comes to host and service discovery. But thanks to the Nmap Scripting Engine (NSE), it can do much more than that, allowing users to easily develop and share their own scripts, thus turning it into a versatile security scanner.

### What you will learn...
* basics of the Lua programming language,
* walkthrough of a simple NSE script,
* where to find more information on scripting Nmap and Lua in general.

### What you should know...
* basic Nmap usage,
* an imperative programming language.

### The Nmap Scripting Engine (NSE)
Scripting has been part of Nmap for a while, but version 6 includes a dramatically improved implementation of the NSE infrastructure. This also led to an enormous increase in [available scripts](http://nmap.org/nsedoc/), from 59 in version 5, to 347 in the latest release and more than 430 in the current SVN repository. The core of NSE is an embedded Lua interpreter. Before NSE’s implementation started, the Nmap developers considered various programming languages for inclusion, but finally settled on Lua because it's small, liberally licensed (MIT), fast, actively developed and designed with embeddability in mind. It's also used in other popular FOSS security tools like [Wireshark](http://www.wireshark.org) and [Snort](http://snort.org/), as well as in popular projects such as *World of Warcraft* or the window manager [Awesome](http://awesome.naquadah.org/).

## The Lua Programming Language
Development of [Lua](http://lua.org/) started in 1993 at the Pontifical Catholic University of Rio de Janeiro, Brazil. It was designed by Roberto Ierusalimschy, Waldemar Celes and Luiz Henrique de Figueiredo, with the primary goals of being lightweight, cross-platform and easy to embed in other projects. These goals were achieved by writing the language in portable ISO C and giving it a comparatively simple C API. Also Lua's interpreter only takes up about 180 kB in compiled form, so embedding it does not incur a huge cost. Over the years it became a popular scripting and extension language, used in a wide variety of projects. The syntax of Lua should be familiar to anyone who has experience in Modula or C style languages (control structures like _if_, _while_, _for_), but the designers borrowed liberally from other languages as well: the semantics of Lua are heavily influenced by Scheme, and even though the different syntax obscures this a bit, it becomes rather clear the longer you use the language. In some ways it feels like a cleaner and better designed JavaScript, so it's normally
easy to understand a given snippet of Lua code even with little or no previous exposure to the language.

### Key features
Lua features first-class functions, thus allowing the developer to treat functions like any other data type. It also doesn't impose a certain programming paradigm, but instead provides meta-features to extend the language as needed. This for example makes it possible to implement things like inheritance in a rather simple and straightforward way without explicit language support. Lua is also garbage collected, features closures and proper tails calls and provides an infrastructure for cooperative multitasking in the form of coroutines. While most of this goes beyond the scope of this article (and NSE), interested readers can find more information in the excellent book "Programming in Lua", [an older version of which is available for free on the project's web site](http://www.lua.org/pil/).

### Tables
There's is however one more feature of Lua that's worth discussing in a bit more detail, namely its reliance on a single compound data structure, the table. Tables are in essence what other languages commonly refer to as associative arrays or dictionaries, or key-value pairs. This is how you create a simple table:

{% codeblock lang:lua %}
{% raw %}
table = { x = 10, y = "foo" }
{% endraw %}
{% endcodeblock %}

The values can be accessed like this: *t["x"]* and *t["y"]*. For string keys Lua supports a nice shorthand though, so *t.x* and *t.y* will produce the same result.

One special feature of Lua's tables is that they are also optimized for use as arrays, which can be created like this:

{% codeblock lang:lua %}
{% raw %}
array = { "citizen", "428", "blog" }
{% endraw %}
{% endcodeblock %}

Indices are assigned automatically and unlike in other languages start from 1, not 0 (although it is possible to explicitly assign to *array[0]*). So to get the string "428" out of our array, we have to use *array[2]*.

With this comparatively simple data structure (and the powerful metatable feature) Lua not only implements arrays and dictionaries, but also records/structs, namespaces, classes, objects and much more.

### Examples
Enough theory, let’s look at some example Lua code. First the customary “Hello world” program:

{% codeblock lang:lua %}
print “Hello world”
{% endcodeblock %}

This will just output *Hello world*. Now for a function definition:

{% codeblock lang:lua %}
function sayHello(name)
  print ("Hello " .. name .. "!")
end
{% endcodeblock %}

To generate the same output as above, we would have to call the function with the appropriate argument: *sayHello(“world”)*. Alternatively, the same function could be defined like this:

{% codeblock lang:lua %}
sayHello = function(name)
  print ("Hello " .. name .. "!")
end
{% endcodeblock %}

Last but not least a small loop that iterates over an array and prints out each element (*ipairs* returns the index-value pairs of an array):

{% codeblock lang:lua %}
for i, str in ipairs(array) do
  print (i..": "..str)
end
{% endcodeblock %}

Assuming the variable *array* contains the elements “citizen”, “428”, and “blog”, this will generate the following output:

    1: citizen
    2: 428
    3: blog

## NSE example script
The Nmap web site contains some excellent documentation regarding NSE ([short intro](http://nmap.org/book/man-nse.html), [full book chapter](http://nmap.org/book/nse.html), [tutorial](http://nmap.org/book/nse-tutorial.html), so instead of repeating all of what’s written there, we’ll look at a practical example instead. The script shown here is [http_generator.nse](http://nmap.org/nsedoc/scripts/http-generator.html), which looks for the presence of an HTTP *generator* meta tag to determine which CMS the scanned hosts run (if any). This information can be useful, since outdated versions of such systems are common attack vectors.

### Library imports
{% codeblock lang:lua %}
local http = require "http"
local shortport = require "shortport"
local stdnse = require "stdnse"
local string = require "string"
{% endcodeblock %}

It's customary to start Nmap scripts by importing all the needed [libraries](http://nmap.org/book/nse-library.html). In the process they are assigned to local variables, so if you wanted to access them under a different (presumably shorter) name you could do so (for example *local s = require “string”*). There's a wide variety of available libraries, from protocols (*afp*, *dns*, *http*, *bitcoin*, *openssl* etc.) over encoding/decoding (*base32*, *base64*, *json*) and utility libraries (*nmap*, *packet*, *pcre*) to a framework for brute-force attacks against services (*brute*).
The script we are looking at uses the *http* library for communication, *shortport* for an easier way to define portrules (more on that later), and *stdnse*, a collection of utility functions provided by NSE. The *string* library is a standard Lua library and used for string matching in this example.

### Metadata
{% codeblock lang:lua %}
description = [[ Displays the contents of the "generator" meta tag of a web page(default: /) if there is one.
]]

author = "Michael Kohl"
license = "Same as Nmap--See http://nmap.org/book/man-legal.html"
categories = {"default", "discovery", "safe"}

---
-- @usage
-- nmap --script http-generator [--script-args http-generator.path=<path>,http-generator.redirects=<number>,...] <host>
--
-- @output
-- PORT    STATE SERVICE
-- 80/tcp  open  http
-- |_http-generator: TYPO3 4.2 CMS
-- 443/tcp open  https
-- |_http-generator: TYPO3 4.2 CMS
--
-- @args http-generator.path Specify the path you want to check for a generator meta tag (default to '/').
-- @args http-generator.redirects Specify the maximum number of redirects to follow (defaults to 3).

-- Changelog:
-- 2011-12-23 Michael Kohl <citizen428@gmail.com>:
--   + Initial version
...
{% endcodeblock %}

After the library imports, scripts contain some metadata that is used for documentation and internal purposes. The *description* and author variables are self-explanatory, the syntax used for the former is Lua's way of creating so-called "heredocs", string literals that preserve white space and line breaks. In regard to the *license* script authors have two choices, the GPL-like Nmap license, as well as a simplified (2-clause) BSD license. Last but not least there's documentation in the NSEDoc format. It's customary to include usage documentation, example output, descriptions of all the available arguments (if any) as well as a changelog.

There's another important field that this specific script doesn't use, *dependencies*. This defines the order in which scripts should be run and is used for the case where one script works on the results of another script. It's important to keep in mind that scripts listed here will not be automatically run, they still have to be selected via *--script scriptname* or in another way (for example *--script=default*).

The *categories* variable is a bit more interesting, it defines which groups of scripts our current program belongs to. Scripts marked as *default* will be run automatically when Nmap is started with the *-sC* or *-A* options.To qualify for inclusion in this category, scripts have to adhere to some criteria: they need to be fast, reliable, non-obstrusive and generally useful. There are no clear definitions for these criteria, it's up to the Nmap developers to add a script to this category. The *discovery* category collects all scripts that actively try to gather more information about the scan targets, in our case the "generator" HTML meta tag. If a script is marked as *safe*, that means that it was not written with the intent of crashing services or exploiting vulnerabilities and that it's unlikely to consume large amounts of resources. There are many more categories Nmap uses, for example *auth* (scripts dealing with authentication credentials), *brute* (tools for brute force attacks) or *vuln* (checking for specific vulnerabilities). For a full list, please consult Nmap's excellent documentation.

Now that we know a bit more about categories, let's see how we can use that knowledge for more interesting Nmap runs. Let’s start by running all the default scripts:

{% codeblock lang:bash %}
nmap -sC example.com
{% endcodeblock %}

or

{% codeblock lang:bash %}
nmap --script=default example.com
{% endcodeblock %}

The invocation *nmap -A example.com* ("Aggressive/advanced scan options") enables OS and version detection, script scanning (equivalent to *nmap -sC*), and traceroute.

If you just want to run a single script, you can do so by specifying the *--script* argument, for example *nmap --script http-generator example.com*.

It’s also possible to run scripts from several categories, by using something like *nmap --script "default,safe" example.com* or *nmap --script "default or safe" example.com*. There is an analogous and operator, so *nmap --script "default AND safe" example.com* will only run scripts that are in all the listed categories.

As an example for a rather complex invocation, the following command will run all scripts from the three specified categories, except for the ones whose name starts with "http-".

{% codeblock lang:bash %}
nmap --script "(default or safe or intrusive) and not http-*" example.com
{% endcodeblock %}

Last but not least it’s also possible to run user scripts which are not part of the standard Nmap distribution, as show in the following example:

{% codeblock lang:bash %}
nmap --script default,banner,/home/user/customscripts example.com
{% endcodeblock %}

The above invocation will run all default scripts, the script named “banner” and all scripts which can be found in the directory */home/user/customscripts*.

### Rule
The *rule* definition is an important part of every NSE script, it determines when the script should run. A rule is a Lua function that returns *true* or *false* to decide whether or not the script's action function will be executed. There are several different rule types, depending on when the script is supposed to run (before or after hosts are scanned, *prerule* and *postrule*) or if the trigger is a specific host or port (*hostrule* and *portrule*).

The rule definition in our example script is very simple since it uses the very handy *shortport* library, which has several predefined rules for common scenarios:

{% codeblock lang:lua %}
rule = shortport.http
{% endcodeblock %}

This will match and return *true* when a given port is likely to be an HTTP port.

### Action
{% codeblock lang:lua %}
action = function(host, port)
    local path = stdnse.get_script_args('http-generator.path') or '/'
    local redirects = tonumber(stdnse.get_script_args('http-generator.redirects')) or 3

    -- Worst case: <meta name=Generator content="Microsoft Word 11">
    local pattern = '<meta name="?generator"? content="([^\"]*)" ?/?>'

    -- make pattern case-insensitive
    pattern = pattern:gsub("%a", function (c)
                  return string.format("[%s%s]", c, string.upper(c))
              end)

    local response = follow_redirects(host, port, path, redirects)
    if ( response and response.body ) then
        return response.body:match(pattern)
    end
end
{% endcodeblock %}

The *action* function is the heart of every NSE script, it contains the code that will be performed when the rule matches. Basically *action* is just another Lua function that accepts the same arguments as the *rule* function (which is not as obvious in our example because of the use of the *shortport.http* rule).

The function starts with checking for the presence of two arguments and specifies defaults in case the user didn’t provide any values for them. Arguments can be passed to the script in the following way:

{% codeblock lang:lua %}
nmap --script http-generator --script-args http-generator.path=/cms,http-generator.redirects=2 example.com
{% endcodeblock %}

They get stored in the table *nmap.registry.args*, but instead of using them directly you should use the function *stdnse.get_script_args* to access them.

After the argument handling, a pattern matching the HTTP "meta" tag is specified. Standard Lua does not include a regular expression library, but uses its own patterns instead. While there is a library providing Perl-compatible regular expressions (PCRE) for Nmap, it seemed like overkill to drag it in for a single expression, so I relied on plain Lua for this, which explains the little workaround used in the next line to make the pattern case insensitive by replacing every letter (the pattern *%a*) with its lower and upper case version.
After that the *follow_redirects* helper function is used to get an HTTP response, and if there is one, its body will be scanned for our pattern.

If there is a match, the function will return the matching string and Nmap will automatically include it in its output as shown here:

    PORT   STATE SERVICE
    80/tcp open  http
    | _http-generator: WordPress.com |

Instead of a string the action can also return tables consisting of name-value pairs which are then formatted in a structured fashion, whereas the return value *nil* generates no output at all.

As you may have noticed I didn’t yet show you the helper function mentioned above, so while it’s not very interesting in itself, I’ll include it for completeness’ sake:

{% codeblock lang:lua %}
local follow_redirects = function(host, port, path, n)
    local loc
    local pattern = "^[hH][tT][tT][pP]/1.[01] 30[12]"
    local response = http.get(host, port, path)
    while (response['status-line'] or ""):match(pattern) and n > 0 do
        n = n - 1
        loc = response.header['location']
        response = http.get_url(loc)
    end
    return response
end
{% endcodeblock %}

This helper function is a simple way to follow HTTP redirects. It first defines a pattern for recognizing the relevant HTTP status codes (301 and 302) before making a request to the specified host, port and path combination. It then checks for the presence of a redirect and if there is one, follows it to retrieve the location information from the new location. When there are no more redirects or the redirect limit (specified in the argument *n*) has been reached, the function returns the full HTTP response to the caller.

### Digging deeper
And that’s all there is to it! Thanks to the excellent infrastructure provided by NSE and all its libraries, writing custom Nmap scripts is a pretty easy and straightforward task. Anybody with a bit of scripting experience should be able to pick up the basics in a very short time and then learn about the finer points while developing scripts. The Nmap development team has assembled some excellent documentation which can be found online at http://nmap.org/book/nse.html. There’s also a great presentation by Fyodor and Nmap co-maintainer David Fifield available [online](http://nmap.org/presentations/BHDC10/) which gets you started on developing your own NSE scripts in less than an hour.

## Summary
Nmap is a popular tool for security and network professionals around the world, and its excellent scripting capabilities make it very versatile. The Nmap Scripting Engine relies on the popular and easy to learn Lua programming language, and provides useful libraries for quickly and efficiently developing your own custom scripts, which can then be shared with the wider Nmap user community.
