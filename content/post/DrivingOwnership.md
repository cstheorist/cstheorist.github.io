---
title: "Driving Ownership"
date: 2020-06-23T07:22:59-04:00
readingtime: 1
draft: false
---

By the end of this article I hope that as the reader you agree with me on the connection between the following three terms:

* Accountability
* Autonomy
* Ownership

Having been in the industry for longer then I care to admit at this point I have had the opportunity to write a lot of code.  Interestingly enough I have not always had the opportunity to operate that code.  Now, for some developers you might ask - why would you want to operate the things you write?  I mean - if you can write code and hand it off to someone willing to keep it running then you why wouldn't you take that deal?  You might ask, can't you still own the outcome of the code you write if you don't operate the software?  From my experience - no.  As delightful as never having to worry about being paged sounds I actually don't believe that you can be a true owner of the outcome of the products that you have helped build.  Instead at best the solutions (code) that you deliver will only represent a best effort approximation at the solution a customer is looking for.  Let's break down why this is...

**Ownership**

There are a few variations on the term ownership.  I like the way ownership is defined on wikipedia - *is the state or fact of exclusive rights and control over property, which may be any asset, including an object, land or real estate, or intellectual property. Ownership involves multiple rights, collectively referred to as title, which may be separated and held by different parties.*  If we move beyond simple possession we can talk about the act of owning - *the fact of taking responsibility for an idea or problem*.

I like to start here because often times when talking about DevOps the first item that comes up is *Service Ownership*.  But it is important to take some time to really internalize what ownership actually means.  I like to think of this in terms of a metaphor's - for example think about how you take care of a car you own as compared to a car you rent?  Would you take a rental car to a car wash?  Or to get an oil change if you notice it is due?  

Jeff Bezos in a slightly different context once wrote: 

*"Long-term thinking is both a requirement and an outcome of true ownership.  Owners are different from tenants.  I know of a couple who rented out their house, and the family who moved in nailed their Christmas tree to the hardwood floors instead of using a tree stand.  Expedient, I suppose, and admittedly these were particularly bad tenants, but no owner would be so short-sighted."* (2003 Letter)

From this let's define some characteristics of service ownership:

* Long term thinking is required.
* Owners think about the maintenance and upkeep of the systems they provide.
* Owners understand (painfully if necessary) the cost of upkeep (run the business costs).
* Owners take proactive actions when possible.

In short there is a lot that goes into ownership.  If we go back to my opening, if you write code and pass it to someone else to run on your behalf we might consider that we are more like renters or tenants.  Despite our best intentions we don't see the cost that others endure to operate the things we build.  We might hear about customer problems but we don't have the visibility into the black box that is operations to understand how to fix these things.

I have a vivid example while I worked at Kiva Systems.  I wrote software that helped to control robots for warehouses that helped companies pick, pack, and ship products.  I wrote lots of code and we had an operations team and a NOC where all of the operations happened.  During my first holiday season working at Kiva Systems my manager or I would see a ticket come in talking about the fact that a customer site experienced a problem.  The result was that robots stopped moving for some period (typically minutes).  At first when I saw these tickets I would go in, find the "root cause", create a fix, and get it deployed.  With this behavior customers problems were fixed but inevitably another bug would come in and this process would repeat.  One day it happened that I was in the NOC explaining what I had found when I happened to hear a call from a customer come in that robots had stopped moving.  What surprised me is what I heard next.  It went something roughly like this: "Thank you customer, the software stopped and has already automatically restarted - this will take about 30 minutes to finish."  Wait - 30 minutes for our JVM process to restart I thought?  I had never considered our restart time or the fact that restarts lead to customer down time.  I left that room, about 5 days later worked with a few others to understand the time it took for this JVM to start, changed some SQL statements from iterative operations to batch operations and moved a bit of code around and delivered a fix.  After a few weeks we hadn't gotten any additional calls for support.  The issue was that I was blind to HOW the system was operated - as a result I was a renter of the car and didn't see all the work our operations team was applying to ensure that the car kept running.  It was only the point at which I observed the operations piece that I discovered a software solution that could lead to better customer outcomes.  In short - to become an owner I needed to understand both the intent of the software I wrote AND how the software ran in practice.

**Accountability**

From Merriam-Webster we find that Accountability is defined as *an obligation or willingness to accept responsibility or to account for one's actions*.  

If we look back at the definition of ownership we see that these two concepts are related - "the fact of taking responsibility" and here "the willingness to accept responsibility".  What is important here is that owners are accountable.  This can take many forms - and I don't intend to debate an organization structure in this post - but what is important is that IF I own not just code but a solution to a customer problem then I need to take accountability to fully understand the solution.  If we go back to my example while at Kiva we *might* argue that we could achieve service ownership even if there was an operations team IFF engineers writing code were willing to spend enough time to fully understand all the nuances of how the operations worked.  In effect we would move from pure Development and Operations teams to more of a Development and SRE model.  In fact, over my first 2 years at Kiva Systems this is exactly what happened because as engineers started to dig in more the operations team started to engage more quickly with the Development team.  Value was quickly seen in how these teams worked together.  While we didn't immediately move to DevOps teams the focus shifted to holding those writing the code accountable for the solution.

And I feel that this is key - to be owners you MUST be accountable.  You MUST fully accept responsibility for the things that you build.

**Autonomy**

To be accountable the actions have to be your own and this is where **autonomy** fits in.  Think about it.  If I told you that you must only use recursion in writing code and then you hit a stack overflow (I have memories of the call depth in many of the early embedded systems I worked on) would you want me to come and question you why that happened?  Probably not and I wouldn't blame you as you didn't have autonomy to easily control for this behavior.  The key here is that each time we make a decision for a service owning team we take away some amount of autonomy in the decision making of that team, that in turn means the team won't feel accountable for that set of decisions and consequently won't achieve service ownership.  Autonomy and Accountability together means that teams can put themselves into a bit of pain but also have all the tools and local decision making necessary to dig get themselves healthy once again!

**Bringing it all together**

While often times there is a lot of talk about DevOps and DevSecOps and the thought that these structures lead to service ownership I think that it is important to actually first understand what leads to ownership.  I don't believe that there is any golden organization structure.  What I do believe is that different structures can influence the amount of autonomy that teams feel but that it is really this sense of autonomy and clear expectations on accountability that can lead to a culture of Service Ownership.  What are your thoughts and experiences?

Finally for those interested in reading other Bezos letters and thinking the following is an easy read:

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=as_ss_li_til&ad_type=product_link&tracking_id=cstheorist-20&language=en_US&marketplace=amazon&region=US&placement=B07VD2XMHQ&asins=B07VD2XMHQ&linkId=806a3f1863b53cdd276543ca72a3495f&show_border=true&link_opens_in_new_window=true"></iframe>