---
title: "Defining the process for Making a Call"
date: 2020-06-26T07:22:59-04:00
readingtime: 1
draft: true
---

In a number of my posts I have spent time talking about influencing others, getting everyone to agree on problems, focusing on WHY, etc.  During a recent discussion I was having someone asked me a really insightful question: "It is great when everyone sees the same problem - but there are still many times that it is difficult to get investment - what can we do?"

Having worked at companies like Salesforce and Amazon I can definitely say this CAN be a challenge.  You might have a great idea on how to save the company money yet in a distributed ownership model (a lot of small DevOps teams, different General Managers, etc) it can be hard to get the needed distributed buy in (investment) to achieve the end goal.

I won't claim to have an answer for all use cases but I do think we can at least learn a little from what others have found.  One starting point we can look at is the book, [Enterprise Architecture as Strategy: Creating a Foundation for Business Execution](https://amzn.to/38cZ0S5), by Jeanne W. Ross, Peter Weill, and David C. Robertson.  Here they explain why it is worth choosing an operating model and how it enables IT organizations to become proactive (and more importantly why not selecting an operating model carries risk).  In the model they lay out there are four quadrants (diversification, coordination, replication, and unification) split on two axis (Process Integration and Process Standardization).

I also think there are a number of characteristics to think about as they relate to very large (company wide) software/platform architectures (that in totality can be seen as an architecture strategy):

* Map out an Architecture Vision.  I am a big fan of Domain modeling (and tools like Event Storming).  I won't go into all the benefits I have seen from this (that will be another post some day) but starting from here can allow a diverse organization to bucket the things being built.  In this specific case take the identified domains and divide them up based on how they *should* be built.  A key here is to get to a single architecture diagram that can fit on a page that engineers can look at, see where the functionality they are being asked to build fits into it, and understands the most important qualities (guardrails) that should apply.
  * Focus on the picture telling a story for each component that shows up.
  * Show where different pieces can (should) conform as part of technology strategy.
  * Show where different pieces can (should) be replicated.
  * For each box it should be easy to explain WHY it is not combined with something else.
    * Note: at this level don't think of boxes as different because of the functionality is different but rather because the governance over the component and the generic "Definition of Done" for that component is inherently different.
* It is important that the Business Leaders create alignment in how to invest relative to this vision.  My observation is that without this alignment every project must independently get buy-in and generally investment will fall within organizational (ownership) boundaries.
* Define a process for openly defining standards.  This can be light-weight and even under specified but it can be helpful to define a form of RFC process the enables the engineers on the ground to propose the things that would help them most.  At this point the goal is to enable engineers to argue about the RFCs that are important to them.  If done right then engineers are often already bought in on how they should be building.
* Agree to Measure Compliance and automate it.  This can be extremely helpful because it creates accountability.  Think about it - if all the engineers agreed that something should be built a certain way or to certain standards (based on an open standards process which is derived from an architecture vision) AND if they are not implementing in that way then likely there is a barrier.  Identify that barrier (as a leader) and help remove it!

Maybe stated more succinctly, once an organization hits a size mapping out an architecture vision can help define a line between pure chaos and lightweight process.  With business alignment on a vision as a means of normalized communication and shared values spend time educating engineers on why the vision is what it is.  When engineers are not building towards it ask them why not (I like to shy away from Ivory Tower Architecture so the focus here is really listening and iterating on the message of the vision).  Create a process by which engineers can work across the organization to self define the standards they believe in most.  Hold leadership accountable to helping their engineers live within those standards.

Like my last post this strategy is most aligned with a corresponding engineering culture that thrives on pushing decision making towards teams.  Like all things this is certainly not the only way to drive consensus but one that I have been working on refining myself in cultures that are driven by influence rather then top down decision making.  