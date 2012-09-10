---
comments: true
date: 2011-03-24 09:40:24
layout: post
slug: riak-postcommit-hook
title: Riak Postcommit Hooks
wordpress_id: 34
categories:
- Erlang
- Key-Value Store
- Programming
- Random Integer
- Riak
---

Riak provides the ability to add functions on a per-bucket basis that will fire pre- and post-commit when updating a Riak bucket (deletes and inserts).  Because deletes also fire the hooks, you will need to check if the X-Riak-Deleted entry exists. In my use-case for Riak, I am using the postcommit hook to create an indexing and sorting system for the content.

Pre-commit hooks can either be Javascript or Erlang, but Postcommits can only be written in Erlang. From the [Riak wiki:](http://wiki.basho.com/display/RIAK/Pre-+and+Post-Commit+Hooks)


> Post-commit hooks can be implemented in Erlang only. The reason for this  restriction is Javascript cannot call Erlang code and, thus, is  prevented from doing anything useful. This restriction will be revisited  when the state of Erlang/Javascript integration is improved.


To add the postcommit hook to a specific bucket, you just need to modify the bucket properties. This can be done in with any of the APIs. To use the rest API to add an Erlang postcommit hook, you can run the following cURL command:

    curl -X PUT -H "Content-type: application/json" -d '{"props": {"postcommit": [{ "mod" : "module_name", "fun" : "function_name" }] }}' http://127.0.0.1:8098/riak/_bucketname_
{:lang="bash"}

Try to remember that the postcommit propery should be a list of mod, fun tuples, that cause me a bit of confusion.

Using Erlang, you can attach a console to riak using the command
    ./riak attach
{:lang="bash"}

Once in the console, you can make a local connection to riak by calling:

    {ok, Client} = riak:local_client().
{:lang="erlang"}

Then, to set the bucket properties, you can use this command:

    Client:set_bucket(<<"bucketname">>, [{postcommit, [{<<"mod">>,<<"module_name">>}, {<<"fun">>,<<"function_name">>}]}]).
{:lang="erlang"}

**EDIT:** I am not sure when it changed, but the format for the hooks is now slightly different. Setting it from the REST API should use the same format (although I haven't tried), but from the Erlang console, you will have to set the bucket hooks differently. Instead of:

    Client:set_bucket(<<"bucketname">>,  [{postcommit, [{<<"mod">>,<<"module_name">>},  {<<"fun">>,<<"function_name">>}]}]).
{:lang="erlang"}

You will need to do the following:

    Client:set_bucket(<<"bucketname">>,  [{struct, [{postcommit, [{<<"mod">>,<<"module_name">>},  {<<"fun">>,<<"function_name">>}]}]}]).
{:lang="erlang"}

Basically, just wrap it in a [{struct, ...}]. I'm not entirely clear on why this has changed, but I would venture a guess that the mochijson2 library has something to do with it, because when decoding a JSON value, code like this:

    { "test" : 5 }
{:lang="js"}

Is returned as a struct from mochijson2:

    {struct, [{<<"test">>, 5}]}
{:lang="erlang"}
