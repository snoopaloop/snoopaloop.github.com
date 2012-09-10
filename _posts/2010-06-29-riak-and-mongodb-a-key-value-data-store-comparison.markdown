---
comments: true
date: 2010-06-29 14:07:16
layout: post
slug: riak-and-mongodb-a-key-value-data-store-comparison
title: 'Riak and MongoDB: A key-value data store comparison'
wordpress_id: 22
categories:
- Key-Value Store
- MongoDB
- Programming
- Random Integer
- Riak
tags:
- cloud computing
- databases
- key value data store
- mongodb
- nosql
- programming
- riak
---

Over the last week or two, I have been working with [Riak](http://riak.basho.com/) and [MongoDB](http://www.mongodb.org/) key-value data stores with the goal of using them for a messaging system to handle many millions of messages between users, and must be able to scale quickly. I will break this comparison of the two into several blog posts to give you an idea of the similarities and differences of both, and how one might use them in a message system.


## Key-Value Data Stores


Both Riak and MongoDB are key value data stores. Compared to a relational database, a key-value store does not have a strict schema and only has a single 'key' to retrieve the data. Data is generally organized into named containers or collections: 'database.collection' in MongoDB and 'buckets' in Riak.

Key-value stores have an advantage over relational databases in different use cases. Because of the simple relationship between the key and the data, key-value data stores tend to be highly-scalable and very fast, a huge advantage over most relational databases. There aren't really any of the complex queries with joins, subqueries, etc. to bung up the performance of your database, and key-value data can be easily spread across multiple servers.

![A Riak cluster, look at the pretty ring!](http://wiki.basho.com/download/attachments/360641/riak-ring.png?version=1&modificationDate=1274305664000)

Riak was designed to scale quickly and easily and has some very interesting features, like replicated nodes with no single node acting as a 'master' node like in a master-slave replication systems. This means that if a node crashes, the other nodes automatically pick up the slack. Retrieving data in Riak uses MapReduce functions, so adding more nodes generally increases the speed of data retrieval. High-availability, scalability and fault-tolerance are the results of this structure.

The upcoming release of MongoDB will have auto-sharding built into the database, in addition to the replication across networks that is already available. Mongo stores the document data in BSON format (very JSON like) and uses GridFS to handle large binary files. But MongoDB also has some of the features users are used to in relational databases like indexes for extremely fast document searches.

So, tune in later for the next post where I will talk about Riak (or maybe MongoDB) and some of the features that stand out. I'll also show some  simple examples for using the databases and how they might apply to a messaging system.
