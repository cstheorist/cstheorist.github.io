---
title: "Building for failure in a distributed world - taking dependencies robustly"
date: 2020-05-30T07:22:59-04:00
readingtime: 1
draft: false
---

I have been working on systems that customers rely on 24x7 my entire career.  A common question that comes up in almost every software design centers around how do we build a piece of software to achieve "100%" availability?  Today I wanted to share a few thoughts that I have on this topic.

**When a system fails everyone needs to understand why**

Something that I have learned over the years is that when a system fails and a customer is impacted it really doesn't matter what piece of hardware or code may have malfunctioned.  What really matters is understanding proximate causes.  Important: I said causes (plural) as I firmly believe that in a robust system there is almost never a singular root cause but rather a set of problems related in some way that ultimately culminated in a failure.  The result is that once a failure has occurred there is no sense in targeting a single WHO (blame) but rather we need to focus on WHY the failure occurred.  I like to approach a problem system failures in the following way:

* Identify HOW the system was broken.  Be crisp on defining what the customer(s) was observing and compare it to what should have happened.
* Ask what system(s) are intended to be in place that should compensate to prevent the HOW.
  * For each compensating system determine if it fulfilled its responsibility.
* Check to see if there were at least 2 compensating systems in place that could have prevented the failure.  If not ask if adding another trigger is appropriate.
* Carefully look at the MTTR and look for any delays that should be mitigated.  This could be through the addition on monitoring and alerting controls.

Notice that in the list above there is little discussion about the specific issue.  Yes, we should understand the bug itself and if it was fixed, but to build a really robust system while enabling fast cycles of iteration the "root cause" is often not the problem - i.e. we should simply expect that some set of bugs WILL be shipped to production, rather we need to consider those proximate causes that allowed the bug that was shipped to have such an impact.  This analysis will span beyond any one individual or team and thus the importance for everyone understanding WHY.

For those interested in some more formal methods for this type of analysis you may want to take a look up "Causal Analysis using System Theory" and STAMP.

**Defining a compensating system**

Think about some of the mechanical systems you are familiar with.  Take a car for example.  What happens when we drive over a pot-hole?  Generally a number of compensating systems come into play such as the suspension system to dampen the impact and effects of the event.  In robotics more research has been going into spring-mass systems to build more robust robotics that can compensate for unknown environments.  

The same holds true in software systems.  A simple example might be what happens as we are watching a video on a mobile device and move between cell-towers.  While there are small drops in the communication path the video can continue playing because we compensated with some amount of buffering content (downloading ahead of what is actually being viewed).  

So - a compensating system is any design that is intended to compensate for a failure.  If we think of pushing a code bug we might consider a set of compensating systems.  First, we likely had some form of code review process where another engineer may have seen the issue.  That may have been followed by some amount of automated testing that ideally could have seen the bug.  Next may have been the rollout strategy (Blue Green, Canary Deployments, etc).  After that should the component have been failing I might look to clients of the component to determine if they could have handled the failure while themselves remaining available.

**What are some methods of compensating**

I will often times hear complaints that some underlying system is not 4 9's available so they themselves won't be able to achieve that goal but more often then not it is simply not true.  In fact, I would argue that in a large distributed system the more things that "can't" fail - the more likely the distributed system is actually hurting the development process (slowing everyone down).  This is because for each thing that simply "can't" fail in production we must either (a) spend significant engineering and cost to replicate behaviors and (b) will find ourselves implementing processes in front of deployments that will slow larger and larger groups down.  Therefore a key method and point to get across is that when building a distributed system you MUST minimize the number of "can't" fail dependencies (I will refer to these as hard dependencies - and we should ideally drive them to 0).

Instead I challenge every developer to look at hard dependencies and ask how they can be turned into "soft dependencies."  A soft dependency is something that can fail for a defined period of time before it will impact the component I have built.  How might you do this you ask?  The following are just a small number of methods that I have found to work well:

* Build a client side cache.  The nice part about a cache is that you have a local copy of a response or data that you may need.  The result is that a dependency can fail and if you have the information you required in your local cache then you can continue to processing with little impact.
* Degrade with your cache.  Often times when we build a cache we want to push things out and refresh from the dependent system.  When that system is failing we may decide to allow cache values to live on longer recognizing that the performance of the system may slowly degrade with the age of the items in the cache but we may avoid direct failure.
* Consider interactions that allow pre-fetching.  Consider a security token of some form that you need in order to place calls to other systems.  Instead of making a call to a security service once the token expires, pre-fetch the next token at some period ahead of the expiration.  Having tokens that overlaps for periods of time allows for an outage of that period.
* Have a backup.  If a dependency is failing can you return a reasonable result?  I like this approach when building out some new feature or service.  I can make attempts to call the "new thing" but fall back to the robust behavior I had previously defined should that new dependency be failing.  This is a great way to approach evolutionary architectures in my opinion.
* Return a default.  If a dependency is failing can you return a simple default response if one of your dependencies is failing?
* Circuit-break.  Ok, at some point a failure may be occurring but recognize that continuing to make the call is likely causing more harm then good.  Consider how you can circuit-break and use one of the above methods to give the service end some time to recover.  This can compensate for dependent systems that might not have implemented some form of rate-limiting or which have a queue in front of them for which additional requests are just blocking and slowing down the total time of recovery.

**Beware of poisoned pills**

I have seen way to many systems built in a reactive pattern with no method of recovery so I felt this was worth an explicit call out.  Consider building a warehouse system where we want to track the quantity of items at different locations in the warehouse.  Many different downstream systems will take a dependency on these events in order to do processing (like telling somewhere where to talk in the warehouse to pick an item).  Therefore we presume that a dependency can build a reasonable picture of the world incorporating this information and if the information is incorrect that those systems will suffer (make poor decisions).  Question: what happens if the producer pushes out a code change and the events being published are incorrect?  Maybe the issue is fixed after 5 minutes but what are clients supposed to do?

I call this out very directly because in many designs I review I often will see teams do fault analysis and consider the failure of the message system itself but very few will also consider data corruption.  The result is that a producer will pump data out onto something like Kafka and consumers will consume.  On substrate failure or a client failure everything looks great, we look to where we want to rewind and start reading again in the event stream.  In effect we don't just rely on the durability of events but we rely on the accuracy.  But here is where poison pills become a problem.  If that data is corrupt everyone will think the system is functioning and once we recognize a failure the stream of data is now poisoned.  The solution is to reset the stream and start over - but that might be expensive.  Going back to our example, what if we never accounted for such a failure.  As a result we might have done a "Zero-day" process to prime our system and then relied on the even stream to ensure accurate updates.

Unfortunately in practice I have found that data corruption is at least as likely as some underlying substrate failure.  As a result I prescribe to the need for a natural method of anti-entropy.  Specifically, when building such event streams we must also consider how we periodically publish a snapshot in time of all data such that anything beyond that time period will automatically be brought back into sync.  Clients then must implement an ability to periodically read the full snapshot and correct any accumulated errors.

Note: for those interested you can also read my post on decoupling systems as you may find that things like durable message streams may not be worth the cost if there is already an anti-entropy mechanism that produces snapshots over short periods of time... food for thought.

**Some final words on taking dependencies robustly**

Consider that everything above are those things clients can (and SHOULD) consider.  I always like to remind a developers that they are accountable for their systems remaining up.  They may want to hold a dependency responsible for helping but they should take the accountability to ensure the component they build is robust as possible.

While I focused on taking dependencies today - don't worry, I will have a few words on how to make sure you expose services in a robust way to your clients coming up soon...