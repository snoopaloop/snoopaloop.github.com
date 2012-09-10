---
comments: true
date: 2010-10-13 14:37:10
layout: post
slug: riakriak-search-setting-bucket-props-and-custom-extractors
title: 'Riak/Riak Search: Setting bucket props and custom extractors'
wordpress_id: 46
categories:
- Programming
- Riak
- Riak Search
tags:
- indexing
- riak
- riak search
---

Phew!

That's a long title for a blog post, but it describes both of two topics I want to write a quick post about when I was tinkering with Riak Search today.

I was working on setting up some Basho Bench tests to check the performance of indexing with Riak Search, where the data inserted into the Riak bucket was a JSON object encoded using the mochijson module. After setting up my bucket indexing schema (see the [schema section in](http://wiki.basho.com/display/RIAK/Riak+Search+-+Schema) the Riak Search wiki page), I kept getting an error saying that my default value, id_num was not set. I guessed it was not not extracting correctly for some reason (probably my own fault!) so I decided to take the opportunity to create a simple custom extractor.

So, first thing first, according to the Riak wiki, a custom extractor using an Erlang module and function (which is what I wanted to use), should return a list of tuples in the format:

    [
      {<<"key1">>, <<"value1">>},
      {<<"key2">>, <<"value2">>}
    ]
{:lang="erlang"}

How convenient! This is the exact format you get back when you parse JSON objects with mochijson. A custom extractor will also be useful if I need to play with the data a little before it gets inserted as an index.

The code I am using as a custom extractor for my particular test data is quite simple:

    -module(custom_extractor).

    -export([extractme/2]).

    extractme(Robj, Args) ->
      {struct, Val} = mochijson2:decode(riak_object:get_value(Robj)),
      Val.
{:lang="erlang"}

The custom extractor needs to take two parameters, the first being the Riak object (from riak_object in riak_kv) and a list of arguments. I don't really care about the arguments for this particular test, so I just toss them aside. All I'm doing in this code is decoding with mochijson2, and returning the list of tuples. Done.

Next, also from the Riak wiki, the [Indexing and Querying Riak KV Data](http://wiki.basho.com/display/RIAK/Riak+Search+-+Indexing+and+Querying+Riak+KV+Data) page states that to add a custom extractor you should change the bucket properties for the indexed bucket. Because I want to use the {modfun, Module, Function} type extractor, I set the 'rs_extractfun' property on the bucket by doing the following from the riaksearch console:

First, attach to the console:

    bin/riaksearch attach
{:lang="bash"}

Next, get a local riak_client connection to the node:


    (rs1@127.0.0.1)60> {ok, Client} = riak:local_client().
    {ok,{riak_client,'rs1@127.0.0.1',<<3,239,88,180>>}}
    (rs1@127.0.0.1)61>
{:lang="erlang"}

Finally, set the bucket properties:

    Client:set_bucket(<<"testbucket">>, [{rs_extractfun, { modfun, custom_extractor, extractme }}]).
{:lang="erlang"}


Done, index away!
