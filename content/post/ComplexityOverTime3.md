---
title: "Minimizing complexity over the life of a system - Part 3"
date: 2020-05-25T10:22:59-04:00
readingtime: 1
draft: false
---

In my previous [post]( {{< relref "ComplexityOverTime" >}} ) I called out 3 areas that stood out to me as adding complexity over time to long lived systems and dug into the first of those points (using a technology without asking WHY it was the right tool).  Today I want to focus on the third, over-simplifying our transactional model (presuming the digital and real worlds move in lock step).

**Over-simplifying our transactional model.**

For context, many of the systems that I work on have some notion of modeling the state of things as they exist in the real world.  In an e-commerce system those things might be the physical inventory, in a warehouse it can include all assets (inventory, robotics, orders, people, and more).  When building software systems a simplifying assumptions I often see made is to model the state of the real world as though it is moving in lock step with the digital world (much like an ATM transaction with our bank).  At first blush this is great as it creates a very simple measure of success relative to the functional requirements relative to a system being built, more specifically we might consider the system to be correct if at any moment in time we look at the system and it looks like the real world.  In simulation and testing this isn't all that hard to prove and is relatively simple environments things appear to work out just fine.  The problem comes when we attempt to scale such systems if our customers have built their systems based on those simplifying assumptions.  Such strict consistency comes at a price in terms of performance, availability and reliability. It is common to see chains of API calls spanning several different software stacks and leveraging a range of technologies.  Consider that the longer the chain of APIs, the greater the likelihood of failure and the slower the overall response times will be.  In other words, the assumption that made it easy to reason about the world has now introduced a constraint that is difficult and often expensive to solve for.

First we need to understand that the digital world is only as good as the observations made.  Let's consider a warehouse that we are attempting to model.  As every order is placed on a website we track the total quantity of inventory that is available.  But what happens if while attempting to fulfill that order we realize that one of the items we intend to ship is broken?  What if a forklift runs over a box?  What if we received the wrong items from the vendor?  What if we accidently picked and shipped two items to a customer and only later found out we were short an item to ship?  In each of these cases it is likely that for some period of time the digital world and the real world were out of sync.  The reality is that the real world is constantly changing and we often are not creating digital events for all things.  At the end of the day this real world is the System of Record and what we represent in the digital world is just a cache.  Once we recognize this simple fact then we can start to determine if there is a better way.

So what might we do early to simplify our lives later?

* Don't leak simplifying assumptions to customers unless they are important to the problem being solved.  The key here is that once something has been baked into an interface it has become 100x more expensive to undue (we have walked through a one-way door).
* Implement the simplest thing but continuously look at how to invest towards the future.
* Look to solutions that bound the likelihood of error - don't attempt to eliminate it.

Let's consider each in a bit more details.

**Don't leak simplifying assumptions**

Having worked at Amazon I often heard the quote: "Strongly held assumptions loosely held."  I like that phrase - thinking in this way is helpful to cut through a lot of fear to make a decision knowing that as we learn something new about the problem(s) we are attempting to solve that we can re-evaluate and change.  A key to being successful is to not build a behavior into an interface with your customers that acts as a one-way door.  We want to look out for assumptions that may be easy to build today but don't directly add value to the problem being solved.

Once we have exposed a behavior, intentional or not, we should presume that the behavior has become relied upon.  Deprecation of such a behavior means working with each of your customers to ensure they understand the change.  Consider popular platforms and the length of time we see "deprecated" methods, APIs, or functions and you can see just how difficult removal can be.  Beyond just the technical challenges you must also deal FUD (Fear, Uncertainty, Doubt) about changes in interaction patterns and the potential impact on optimizations.  

> A number of years ago I worked on a system that presumed that every asset in a warehouse was being discretely tracked.  It was ok that an occasional error might be discovered but there was a strongly held belief that the optimality of the solution was based directly on the freshness of information fed to that algorithm and so every effort was made to track and represent changes in state as quickly as possible (ideally in under a second).  One day I was asked how we might deploy this system into a warehouse that was 10x the largest warehouse we had ever seen.  When we looked at the rate at which information would be changing and did some back of the napkin math it was pretty clear that moving that much data around was going to be both expensive and complicated.  Despite my many suggestions that we should take a different approach there was a strong desire to continue down the well known path.
>
> I was lucky enough to be in a position to go off and run a few simulations.  My approach was simple, force a time misalignment between the real world and the digital and see the impact on the decisions that the algorithms would make.  In the end we found that, for the specific algorithms in question, they could tolerate information drift of nearly 10 minutes before there was any statistical degradation in performance.    That length in time was even a surprise to me.  Now here is some irony, the result was so good that no one wanted to believe it.  In fact a new fear emerged that perhaps the simulations we not as predictive of the world as once thought.  Ultimately we started injecting chaos into a live system and also observed no change in performance.  The moral of the story is that long held beliefs are often difficult to change.

So, my first bit of advice is simply to not leak assumptions as they relate to timeliness and accuracy of information in an interface until it becomes important to the problem you are attempting to solve.

**Implement simple but be ready to change**

Assuming that we have thought carefully about our interface then what should we implement?  Do I need to implement a more expensive solution up front?  I would argue no.  I would start building behind the interface the simplest thing possible as long as we are able to continuously modify and change it.  Derived from the previous conversation there are a few important considerations here:

* APIs are frequently more difficult to change then internal details because you need to change both your implementation and work with all of your clients to consume a change in the interface.  Focus attention there and try to make sure those interfaces have some life in them.
* Don't underestimate the difficulty of changing out your datastore without your customer knowing.  This is far easier if you can think of your database itself as a "cache" (derived from) some other system of record.

My mental model here is to not over think interfaces between different concerns in your solution but also do not underestimate the value of having some interface.

**Bound error - don't strive for perfection**

When dealing with IoT systems I like to think of different bubbles of correctness.  If I am attempting to orchestrate over a large set of devices I want to be able to have information that is likely to be correct within a timeframe that can be reasoned about (or predicted).  The goal here is to make optimistic decisions about what we would like to have happen next.  This forms our outer bubble.  Inside this bubble we might want to perform some level of local optimization.  We want to deal with data that has higher fidelity.  Finally we have our inner-most bubble which is the set of algorithms for which we want to have a high degree of predictability even at the cost of some external optimization.  Consider the following example and the relationship to these three bubbles:

* Air-traffic control for drones.  We want to get as many drones from their current position to a destination as quickly as possible without crashing (running into static objects like a tree or dynamic object like other drones).  While we need information about where each drone is with some accuracy to guide them we might think about how we can do so in terms of waypoints.  This algorithm can function in the outermost bubble and assuming that there are not outside acting forces we can model the world and make periodic observations to ensure our assumptions have held true.  (Some may see a similarity in computer science to optimistic concurrence control).
* Drones flight system.  We want to ensure that the drone moves between waypoints in a direct path unless it needs to avoid something.  This forms our middle bubble.  We might have a camera system that is looking out for information with a high degree of fidelity but the algorithms we use and the results produced might deal with some information loss or in-accuracy.  We might also be willing to use algorithms that don't produce a provably predictable result (like a machine learning algorithm) but function well at dealing with previously un-modeled environments in optimal ways.
* Drone safety system.  While we are moving between waypoints we want to make sure we don't crash into an object.  This system can deal with inputs but the accuracy is what is important to us.  We no longer care about other measures of optimal movement, we care that if we need to stop or re-trace our steps that we can do so.  The result of such systems in practice need to undergo extensive testing to ensure that the outcomes can be predicted with a great deal of certainty.

As it relates to building robust systems what we don't want to do is mix the three bubbles.  Rather, if we understand the environment in which we live and can encapsulate competing concerns into their appropriate bubble then we don't need to solve for all requirements in all places which I contend is a drive of complexity over time.  We can also consider how this relates to the "Slices" and "Layers" terms I wrote about in [part 2]({{<relref ComplexityOverTime2>}}) yesterday.

**What have you learned**

While these are some of the lessons that I have learned over the years - what have you learned and what else as engineers should we be thinking more about?