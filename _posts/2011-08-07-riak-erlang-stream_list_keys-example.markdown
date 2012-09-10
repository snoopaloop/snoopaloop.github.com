---
comments: true
date: 2011-08-07 21:09:47
layout: post
slug: riak-erlang-stream_list_keys-example
title: Riak erlang stream_list_keys example
wordpress_id: 70
categories:
- Erlang
- Programming
- Riak
---

I'm not sure how long ago I forgot about this, but I just remembered about a nice little feature when I wasmoving some data around on a Riak cluster. As the post title points out, this feature is streaming list keys in a bucket. Since I like to write lots of little helper code when messing around with data in Riak, this quick little example shows how riak_client:stream_list_keys() can work:

    -module(stream_keys).

    -export([stream_it/1]).

    stream_it(Bkt) ->
    %% step 1: get a client, step 2: call stream_list_keys with that client
    %% step 3: ??? step 4: profit!
      {ok, Client} = riak:local_client(),
      {ok, ReqId} = Client:stream_list_keys(Bkt),
      %% call your loop function to keep receiving keys as they stream in
      loop(ReqId, Client, Bkt, []).

    loop(ReqId, Client, _Bkt, Acc) ->
       receive
        {_, {keys, A}} ->
          %% here, A will be a list of 1 key, many keys, or an empty list
          %% In this case, just append the key to the accumulator, otherwise
          %% you could do some other operations
          loop(ReqId, Client, _Bkt, Acc ++ [A]);
        {_, done} ->
          %% we've received the done message, so output our list!
          io:format("done\n~p", [lists:flatten(Acc)])
      end.
{:lang="erlang"}

This is super-simple to work with in Erlang, and the riakc (protocol buffer) code will work nearly the same (I assume anyway!).

The useful bit of this is because list_keys can be a lengthy operation, you can begin doing work on the data before you receive all of the keys.
