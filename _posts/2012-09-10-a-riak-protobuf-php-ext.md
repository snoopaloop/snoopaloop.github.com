---
comments: true
layout: post
categories:
- PHP
- Programming
- Riak
- Protocol Buffers
- PHP Extensions
---

<h3>A real Riak Protobuf PHP Extension</h3>

After a few failed starts, over the last few weeks I've been making some actual progress on a real, working PHP extension for the Riak protocol buffer interface. When I first started working on it (quite some time ago), I had no experience working with PHP extensions and was exceptionally rusty with c/c++. 

After getting frustrated time and again, I had taken a break (read: given up) for the time being on writing this extension. However, at work I found some issues with a particular extension we were working with, and decided that I would give another shot at writing a much simpler extension. The result is a simple [WebDAV extension]("http://github.com/tnc/TncWebdav") that we us internally to move some images around.

I found out that some problems that had plagued me before were actually quite simple. For example, if you use:

    RETURN_STRING(uri, 1);
{:lang="c"}

You had better make sure you have 1 as the second parameter if you don't want the Zend engine to blast your pointers into oblivion.

I also had discovered that it is much, much easier to return an object from a function call on another object. For example, returning a RiakObject from RiakClient::get():

    PHP_METHOD(RiakClient, get)
    {
      riakc_object *intern;
    
      ...
    
      object_init_ex(return_value, riakc_object_ce);
      intern = (riakc_object *)zend_objects_get_address(return_value TSRMLS_CC);
    }
{:lang="c"}

_return\_value_ is a _zval_ that is defined in the PHP_METHOD macro. If you initialize _return\_value_ to anything other than NULL, it will return that value without explicitly returning it. We initialize _return\_value_ using the _object\_init\_ex_ function. The second parameter is a _zend\_class\_entry_ that we previously initialized.

<h3>So?</h3>

Because I have more difficulty writing very good c/c++ code, I found it should be much easier to maintain and cause less pains to use if I simple wrapped the [riak-cxx-client]("http://github.com/basho/riak-cxx-client" "Riak C++ client") and minimized the amount of c/c++ code I needed to write. Currently, I've implemented quite a few functions, but they are very simple and don't account for many of the optional parameters that they need to handle. 

One of the tricky parts will be figuring out how to handle siblings when _allow\_mult_ is on. When I worked with Riak I tried to avoid that situation in the first place (I really didn't want to do conflict resolution), but I have no doubt others will want to do it.

Some functions are pretty much complete, for example:

    PHP_METHOD(RiakClient, del)
    {
    	char *bucket;
    	char *key;
	
      long dw;
      bool result;
	
    	int bucket_len, key_len;
    	riakc_client *client;
	
    	if(FAILURE == zend_parse_parameters(ZEND_NUM_ARGS() TSRMLS_CC, "ss|l", &bucket, &bucket_len, &key, &key_len, &dw))
    	{
    		return;
    	}
	
    	client = (riakc_client*)zend_object_store_get_object(getThis() TSRMLS_CC);
    	std::string bkt = string(reinterpret_cast<const char*>(bucket));
      std::string k = string(reinterpret_cast<const char*>(key));
  
      result = client->c->del(bkt, k, (int)dw);
  
      if(true == result) 
      {
        RETURN_TRUE;
      }
  
      RETURN_FALSE;
    }
{:lang="c++"}

Once I have more of the boilerplate code cleaned up and have added most of the optional parameters to the function calls, I will put this up on my Github page for all to enjoy and hopefully not break your apps.