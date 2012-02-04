---
layout: post
title: "Fun With Twitter and MongoDB"
date: 2011-10-28 20:36
comments: true
categories:
  - programming
  - mongodb
---
I'm currently reading the early access version of [MongoDB in Action](http://www.manning.com/banker/), so I felt like playing around with it a bit (in fact I do use it for [Happynerds.net](http://happynerds.net), but that's not very exciting). I then remembered that I recently stumbled upon a service called [The Exporter](http://export.synack.me/), which I had used to download all my tweets from Twitter (as of October 4th 2011). So why not use MongoDB to find out which users I retweet most often?

<!-- more -->

Since the data provided by The Exporter is already in JSON format, it should be really easy to import it using the `mongoimport` tool:

{% codeblock lang:bash %}
mongoimport -d twitter -c tweets twitter-statuses-citizen428.json
{% endcodeblock %}

Alas this just threw a lot of errors at me and said that no records got imported. A quick look at the [relevant documentation](http://www.mongodb.org/display/DOCS/Import+Export+Tools) shows that `mongoimport` expects one JSON object per line, so I had to remove the opening and closing bracket on the first and last line respectively, as well as the commas separating the lines (`s/,$//`). Once I did this, the import worked as expected and I had several thousand tweets in the collection `tweets` in the database `twitter`.

Since I'll most likely do similar imports in the future, I added a [unique index](http://www.mongodb.org/display/DOCS/Indexes#Indexes-UniqueIndexes) on the field named `id` (note that this is different from Mongo's built in `_id`):

{% codeblock lang:javascript %}
db.tweets.ensureIndex({id: 1}, {unique: true});
{% endcodeblock %}

I also added a [sparse index](http://www.mongodb.org/display/DOCS/Indexes#Indexes-SparseIndexes) on `in_reply_to_screen_name`, the most important field for what I was about to do:

{% codeblock lang:javascript %}
db.tweets.ensureIndex({in_reply_to_screen_name: 1}, {sparse: true});
{% endcodeblock %}

For getting the actual data I wanted, MongoDB's built-in [map/reduce functionality](http://www.mongodb.org/display/DOCS/MapReduce) seemed like a perfect fit. Here's the mapping function, in all it's glory:

{% codeblock lang:javascript %}
m = function () {
  emit({user:this.in_reply_to_screen_name}, {count:1});
}
{% endcodeblock %}

The reduce step is equally simple:

{% codeblock lang:javascript %}
r = function (k, v) {
  var count = 0;
  v.forEach(function (value) {count += value.count;});
  return {count:count};
}
{% endcodeblock %}

To make the actual `mapReduce` invocation more readable, I decided to also save the query in a separate object:

{% codeblock lang:javascript %}
q = { "in_reply_to_screen_name" : { "$ne" : null } }
{% endcodeblock %}

With all the pieces in place, I could run `mapReduce` to put the result in a new collection named `top_retweets`:

{% codeblock lang:javascript %}
db.tweets.mapReduce(m, r, {out: "top_retweets", query: q});
{% endcodeblock %}

Querying the freshly created collection, sorting it in reverse order of `value.count` and limiting the result set to 10 elements gave me the top 10 users I retweet (note that this data is most likely not entirely accurate, since The Exporter only got me around 3.2k of the roughly 5k tweets I had at that time):

{% codeblock lang:javascript %}
db.top_retweets.find({}).sort({"value.count":-1}).limit(10);
{ "_id" : { "user" : "MrJaba" }, "value" : { "count" : 53 } }
{ "_id" : { "user" : "leyrer" }, "value" : { "count" : 52 } }
{ "_id" : { "user" : "cypher" }, "value" : { "count" : 38 } }
{ "_id" : { "user" : "MSch" }, "value" : { "count" : 34 } }
{ "_id" : { "user" : "kewagi" }, "value" : { "count" : 25 } }
{ "_id" : { "user" : "IORayne" }, "value" : { "count" : 23 } }
{ "_id" : { "user" : "berenguel" }, "value" : { "count" : 22 } }
{ "_id" : { "user" : "kaleidic" }, "value" : { "count" : 20 } }
{ "_id" : { "user" : "antifuchs" }, "value" : { "count" : 19 } }
{ "_id" : { "user" : "ofalk" }, "value" : { "count" : 19 } }
{% endcodeblock %}

Hm, this is a collection I'll most likely query more in the future, how did the above query perform? Not so well I'm afraid:

{% codeblock lang:javascript %}
db.top_retweets.find({}).sort({"value.count":-1}).limit(10).explain();
{
	"cursor" : "BasicCursor",
	"nscanned" : 192,
	"nscannedObjects" : 192,
	"n" : 10,
	"scanAndOrder" : true,
	"millis" : 0,
	"nYields" : 0,
	"nChunkSkips" : 0,
	"isMultiKey" : false,
	"indexOnly" : false,
	"indexBounds" : {

	}
}
{% endcodeblock %}

The most important thing to note here is the difference between the number of documents (`nscanned`, 192) and the actual number returned (`n`, 10). Also the query had to order the result for us (indicated by `"scanAndOrder" : true`). Fortunately that's nothing another index can't fix, this time a reverse one on `value.count`:

{% codeblock lang:javascript %}
db.top_retweets.ensureIndex({"value.count": -1});
db.top_retweets.find({}).sort({"value.count":-1}).limit(10).explain();
{
	"cursor" : "BtreeCursor value.count_-1",
	"nscanned" : 10,
	"nscannedObjects" : 10,
	"n" : 10,
	"millis" : 0,
	"nYields" : 0,
	"nChunkSkips" : 0,
	"isMultiKey" : false,
	"indexOnly" : false,
	"indexBounds" : {
		"value.count" : [
			[
				{
					"$maxElement" : 1
				},
				{
					"$minElement" : 1
				}
			]
		]
	}
}
{% endcodeblock %}

All in all this was a pretty fun little exercise, that proves how easy it is to go from JSON to queryable data with MongoDB. I'll probably do more of this in the future.
