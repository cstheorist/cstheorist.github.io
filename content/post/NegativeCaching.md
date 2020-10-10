---
title: "Some thoughts on Negative Caching"
date: 2020-05-27T07:22:59-04:00
readingtime: 1
draft: false
tags: ["Service Protection", "Architecture Patterns", "What have you seen?"]
---

Over the past few years I have been working on APIs that are public facing.  The implication is that I have little control over what messages may hit a service that I build.  That doesn't mean that there are not simple things that I can do like implement forms of authentication near the edge to reduce traffic to servers but what it does mean is that I don't have control over a customer failing to implement some form of back-off and retry mechanism or what style of query a validated customer may attempt to run.  So, for the moment, let us at least presume that only traffic from a known good customer is hitting my API and focus in on a specific class of use cases that can still cause pain - making calls for non-existent resources.

Ok, some may ask what the big deal is?  Just scale your system out, handle those requests, and return that you didn't find something when needed.  I suppose scaling out horizontally could be one solution; but what if the call for the resource is backed by a Database, holds onto some form of lock, requires some logic to compute how to look up the resource, or any number of other things?  As soon as we are not talking about the simplest of key/value responses the cost of serving these seemingly simple requests grows quickly.

To make this more concrete - assume that 99.999% of the time customers are calling for known objects.  Let us also assume that there are 1 billion objects that a customer could directly address.  Perhaps customers are calling at a rate of 10,000 requests per second.  Finally let us assume that the call behavior fits an LRU cache reasonably well such that we can get an 80% cache hit rate.  Under those conditions our system is effectively processing 20% of 10,000 or 2,000 calls per second when all things are going well.  Now let us add one more condition, on a failed call the customers logic will call again in the next second.  Under normal conditions we wouldn't expect anything to fail.

Ok - so you built this beautiful system and things have worked out well for you.  Then one day that customer deploys some code that bit-shifts everything by 1.  Suddenly non of the resources that they are requesting exist.  The result is that every call comes into the system and attempts to look up the key in our database, fails, and returns to the customer the failure (resource not found).  The customers logic sees the failure and the next second attempts to make the call again and so on and so on.  Using the previous assumptions the first second there were 10,000 calls.  The next second we might see 20,000 calls (the retries for the first 10K and the typical calls for the next 10K).  The 3rd second maybe this is 30,000 calls.  After 1 minute we just received 600,000 calls in a single second.  And each one of these calls continues to go to the database to attempt the lookup and fails.

What happened and why did this go so wrong?  The issue is with how we cache.  We tend to cache those things for which we find some data and don't spend much time considering those things that do not.  We tend to write some code as follows:

```
value = cache.get(key)
if (value == null)
  value = db.get(key)
  if (v != null)
    cache.put(key, value)
```

Ok - this example may seem extreme, after all, the calls are likely being driven from some source and that source likely has limits (like the number of processing threads).  This is all true but what is surprising me more and more is that with K8 and Lambda architectures the clients ("callers") are not carefully thinking about this and service providers are many times not prepared for just how quickly a small failure can escalate.

I see two reasonable approaches for solving for this condition:

* Rate Limiting on the service side is a solid choice that can help.  I suspect that will work well for a large set of use cases and would bound the pain felt on the server.  In a multi-tenant service this would enable other tenants to function.  But this isn't always perfect, if we think back to the previous example perhaps only 20,000 of the 1 Billion requests are for bad objects and those retries keep coming.  After a long enough period there are 20,000 failing calls being processed every second in addition to the normal workload.  If we had rate limited at 15,000 requests per second (50% greater then the peak) we might not see any of the good requests during a given second (or assuming random distributions there are 30,000 total requests of which only 10,000 are real so about 1/3 returns something useful which is about 5,000 - unfortunately the next second the 5,000 that were limited are not only adding more pressure to our already suffering system).
* Negative Caching seems like another solution here but interestingly enough I don't see it talked about very frequently.  Negative caching acts just like any other cache but is intended to cache the fact that there is no data found (or that some error occurred in attempting to retrieve that data).  In this case we could cache the failed lookups, even if for short periods of time, to provide relief to respond to requests customers care about most.  

Where do we see Negative Caching in use today you might ask?  DNS is the most common example that you will probably see referenced but you might also look to CDNs that cache some 4xx and 5xx responses.  It is a helpful little pattern that can unload a great deal of potential traffic for relatively little cost (just cache the fact that on a given set of inputs an error will occur).  

Perhaps at this point you agree that negative caching can be a nifty tool for your toolbox.  I do want to point out a few important things to remember and most of these hold true for any cache.

First, we need to consider the lifecycle of the negative values that we are going to cache.  This is a common place to get tripped up.  That is in part because of the nature and reason that we are attempting to leverage a negative cache.  Consider for example that we want to cache error responses (5xx).  5xx errors imply some form of server error.  As a result, once our server is working again we would want to quickly compensate and flush our cache.  There are a number of different forms this can take from simple (just keep a short TTL for cached values), to much more complicated solutions (such as periodically spot checking some values and flushing portions of the cache once the underlying issue is resolved).

Next we need to consider the size of the cache.  Consider the fact that if you are attempting to protect against invalid inputs that there are likely an infinite number of such inputs compared to the likely countable number of valid keys.

Finally, we need to understand the interaction between positive and negative use cases.  As I pointed out negative caches can have a side effect because there are generally infinite keys that can produce NULL results and countable keys that will produce a valid result.  The consequence is that if we treat both the positive and negative results in a single cache we may find things like Most Frequently Used values are flushed from the cache.  One solution is to separate out positive and negative caches (separate out the use cases) so that each can be independently bound in size, managed different expiration times, etc.  Yes, it is a bit more work but it can lead to more predictable results.  In such cases you can attempt to lookup from your positive cache, on miss check the negative cache, on miss attempt to load.

Overall I think that Negative Caching deserves a bit more open discussion.

I am interested in what others have seen.  Do you too see that with more horizontally scaled compute infrastructure and PaaS layers that small mistakes can ripple into a large Tsunami?  Have you taken a different approach to compensating for clients you don't have control over? 

