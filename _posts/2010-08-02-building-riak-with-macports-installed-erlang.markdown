---
comments: true
date: 2010-08-02 18:30:58
layout: post
slug: building-riak-with-macports-installed-erlang
title: Building Riak with Macports-installed Erlang
wordpress_id: 33
categories:
- Erlang
- Programming
- Riak
tags:
- 64-bit
- building
- darwin
- erlang
- i386
- i686
- install
- macports
- riak
---

In a nutshell, don't do it. I kept getting this error when trying to build [Riak](http://riak.basho.com/) (0.12.0) 64-bit on top of [Erlang](http://www.erlang.org) (R13Bo4) from Macports.

After downloading Riak source, I kept getting the error:

    src/riak_kn_pb_socket.erl: 194 field contents undefined in record rpbputresp
    src/riak_kv_pb_socket.erl: 193: Warning: variable 'PbContents' is unused...
{:lang="bash"}

After hopping on the #riak channel at irc.freenode.net, someone suggested that I build Erlang from either [Homebrew](http://mxcl.github.com/homebrew/) or build from source. I am trusting package managers for Mac less and less, so I decided to build from source. However, I did not configure Erlang correctly, and it built using the 32-bit libraries (I WANT 64-bit!!!). The configure options for building Erlang for 64-bit that worked for me are as follows:

    ./configure --enable-kernel-poll --enable-threads --enable-smp-support --enable-hipe --with-ssl=/opt/local --enable-m64-build --enable-darwin-64bit --build=i686-apple-darwin10
{:lang="bash"}

After I made sure Riak was looking for the library files in /usr/local/lib instead of /opt/local/lib, I was able to run a make all (and make rel) to build Riak.

Note: if you get the error:

/Users/joseph/Downloads/otp_src_R13B04/bootstrap/bin/erl: line 28: $PATH/otp_src_R13B04/bin/i386-apple-darwin10.2.0/erlexec: No such file or directory
/Users/joseph/Downloads/otp_src_R13B04/bootstrap/bin/erl: line 28: exec: $PATH/otp_src_R13B04/bin/i386-apple-darwin10.2.0/erlexec: cannot execute: No such file or directory
make[3]: *** [../ebin/hipe_rtl.beam] Error 126
make[2]: *** [opt] Error 2
make[1]: *** [opt] Error 2
make: *** [secondary_bootstrap_build] Error 2

When trying to build Erlang, check the bootstrap erl executable in bootstrap/bin/ and make sure that the generated url there says i686 and not i386 as above.
