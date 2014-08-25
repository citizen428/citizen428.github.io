---
layout: post
title: Erlang bit syntax and ID3
date: 2010-9-4
comments: true
categories:
- erlang
- polyglot
- programming
---
A couple of days ago I finally started properly looking at [Erlang](http://www.erlang.org/) for the first time. One aspect I find especially interesting is the [bit syntax](http://www.erlang.org/documentation/doc-5.6/doc/programming_examples/bit_syntax.html), so I wrote a small program for parsing [ID3v1 tags](http://www.id3.org/ID3v1) for practice. There’s definitely room for improvement (I ignored ID3v1.1), but it was a fun little exercise.
Here’s the code:

{% codeblock lang:erlang %}
-module(mp3).
-export([get_id3/1, get_tags/2]).

get_id3(File) ->
    case file:open(File, [read, binary]) of
        {ok, MP3} ->
            Result = case file:pread(MP3, {eof, -128}, 128) of
                {eof} -> eof;
                {error, Reason} -> Reason;
                {ok, <<"TAG", Tags/binary>>} -> parse_id3(Tags);
                {ok, _} -> no_id3
            end,
            file:close(MP3),
            Result;
        {error, Reason} -> Reason
    end.

get_tags(Tags, L) ->
    lists:map(fun (Tag) -> proplists:get_value(Tag, L) end, Tags).

parse_id3(<<T:30/binary,Ar:30/binary,Al:30/binary,Y:4/binary,C:30/binary,G:1/binary>>) ->
    Clean = lists:map(fun cleanup/1, [T, Ar, Al, Y, C, G]),
    {id3v1, lists:zip([title, artist, album, year, comment, genre], Clean)}.

cleanup(T) ->
    lists:filter(fun(X) -> X =/= 0 end, binary_to_list(T)).
{% endcodeblock %}

Lets see this in action in the Erlang shell (the MP3 comes from a similar exercise in [RubyLearning’s core Ruby course](http://rubylearning.org/class/course/category.php?id=2)):

{% codeblock lang:erlang %}
147> % file doesn’t exist
147> mp3:get_id3(“./test.txt”).
enoent
148> % file is not an MP3
148> mp3:get_id3(“./test.clj”).
no_id3
150> % get the tags
150> {id3v1, Tags} = mp3:get_id3(“song.mp3”).
{id3v1,[{title,“Dancing Shoes”},
 {artist,“Cliff Richard and The Shadows”},
 {album,“(SUMMER HOLIDAY 1963)”},
 {year,“2000”},
 {comment,“Rubylearningr”},
 {genre,[24]}]}
{% endcodeblock %}

I’m too new to Erlang to judge if this is a proper use of a [property list](http://www.erlang.org/doc/man/proplists.html), but it allowed me to write <em>get_tags/2</em> as a wrapper for
<em>proplists:get_value/2</em> which is rather nice:

{% codeblock lang:erlang %}
151> mp3:get_tags([artist], Tags).
[“Cliff Richard and The Shadows”]
152> mp3:get_tags([artist, year], Tags).
[“Cliff Richard and The Shadows”,“2000”]
{% endcodeblock %}

Some initial help came from [this related blog post](http://bigdingus.com/2007/05/27/id3v1-tags-via-erlang/), but I think our versions came out quite differently in the end.

All in all Erlang feels quite nice, except for minor syntactic quirks like different statement modifiers depending on context or the need to “extract” a local function with <em>fun</em> for the call in <em>lists:map/2</em>. Any feedback would be much appreciated, I’m sure there’s plenty of things I could have done better.
