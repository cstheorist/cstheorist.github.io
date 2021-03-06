---
title: "Minimizing complexity over the life of a system - Part 2"
date: 2020-05-24T10:22:59-04:00
readingtime: 1
draft: false
---

In my previous [post]( {{< relref "ComplexityOverTime" >}} ) I called out 3 areas that stood out to me as adding complexity over time to long lived systems and dug into the first of those points (using a technology without asking WHY it was the right tool).  Today I want to focus on the second, not thinking about where to build partitions (slices and layers) within the solution space.

**Not thinking about where to build partitions (slices and layers) within the solution space.**

I have worked on dozens of systems (and many monoliths) each built on millions of lines of code.  For better or for worse many of these have become large balls of mud in one way, shape, or form.  Not all is lost, I do often find good separation between some concerns.  For example, it is fairly common to see separation between an application tier and a data tier.  Many web application systems built over the years have also created reasonable separation between some set of front end (presentation layer) concerns and those of a backend.  Architecturally we might recognize these systems as 2-Tier, 3-Tier, N-Tier architectures.

I think these are great constructs but are they always enough?  They certainly serve us well early on in a project when we can keep the scope of what we are building in our head but over a long enough period of time we often continue to expand the scope of what we were originally building and one (or more) of those layers grows into something far less manageable.  There are a great deal many consequences that can occur over time.  For the moment let's presume that is our application layer that has continued to grow:

* Build times increase.
* Cyclic dependencies that lead to difficult to understand ripple effects.
* Difficulty testing all parts of the system.
* Difficulty onboarding new developers.
* Lower feature velocity due to all of the above.

Now, some will say this is why micro-services exist.  First let me stop anyone thinking that right here - while micro-services might help they are not a solution by themselves.  For example:

* The micro-service you build today may just be the seed of a monolith tomorrow.
* You can easily build cycles of logic with Micro-Services.
* Micro-Services often lead to polyglot styles of development which can make it difficult to onboard new developers due to the variety of tools, technologies and independent moving components in play.

In other words, micro-services don't themselves solve for the complexities of not defining layers.  What micro-services can do is segregate resources given developers blast radius protections from each other (fault isolation), allow for smaller independent deployments, ease of understanding any given service (the bounds are explicit).

Ok, I digress for now as this post is not intended to argue for or against micro-services but rather to understand a key to keeping a handle on complexity.  For those who have never read it you should look at [The Art of Scalability](https://amzn.to/3ghXCRs)  In this book the authors introduce the "Scale Cube" where:

* the X-axis represents horizontal application scaling
* the Z-axis represents data partitioning
* the Y-axis represents functional decomposition

I suspect that as engineers we are fairly comfortable with the X-axis (load balance over more copies of our application) and the Z-axis (Partitioning tenants/customers onto different instances which we might call cells/pods/zones/regions).  The Y-axis might be less obvious and where I often see not enough attention or focus is made.  I suspect that some reading this will claim that the style of programming they are utilizing is already helping them (such as OOP or Functional Programming) and are therefore taking functional decomposition into account but I suspect what is really happening is that we as engineers are using a language (like Java) or framework (Akka, React, etc) and thinking less about the overall modularity of the system we are building.  In other words, as in previous posts of mine I often find we conflate the use of a tool with the intent of the process of defining our system (why we wanted to use the tool to start with).

At this point I want to step back and acknowledge a simple truth in Computer Science - that naming things is hard.  As a result I need to take a moment to better define what I mean when I say "Functional Decomposition" to make sure everyone reading this is on the same page.  When I use the term **Functional Decomposition** I am referring to *any* division of functionality such that two teams (or parts of an organization) can function independently of each other.  There are two general forms of Functions that we can consider further:

* **Slices** - these are the vertical partitions that we might form between different sets of business logic.
* **Layers** - these are the horizontal partitions that we might form within any slice.  Layers may or may not be shared between slices.

Given this context let's explore how and where we might find such divisions.

**Domain Driven Analysis and Design**

A popular technique for helping discover where possible functional divisions exist is to go through the exercise of Domain Driven Design.  There are many books written on this topic and I will link a few to the bottom of this post.  One important tool to consider here is that of Event Storming as a method of looking to group like things together.  This method works well to discover **slices** of business logic that are worth grouping together and building.  What I like about this Domain Driven Design is that the result can help teams understand their role and function in larger organizations.  It forces the definition of a Language and the bounds (context) in which those terms hold meaning.  If you dive into this model yourself one of the most important concepts is that of the Bounded Context as this is what forms the logical boundaries.

There are a few words of caution:

* in practice we need to be careful to avoid the creation of anemic domain models.  The models tend to focus more on the shape of an object(s) rather then on business behaviors that deliver value.  The consequence is more overhead and complexity in the system(s) we build
* in practice be careful not to build domains that are too small.  In some ways this follows from the previous point but in general I tend to like to think about how we break systems into domains that we could imagine selling to customers (the domains have value and mission).  I have found that when a domain doesn't serve a customer mission it is often neglected, is seen as a cost center, and eventually any behaviors are simply duplicated elsewhere.

One final word on DDD - it is far to easy to move from the design to implementation without considering the volatility of different decisions.  In other words, if we stick only with slices we are asking teams to solve for all horizontal concerns (security, data management, horizontal scaling, networking, observability, and more) on their own.  Therefore we must also consider Layers that are worth introducing.

**Solving for horizontal concerns**

Cloud computing has changed the landscape for how we can approach solving for horizontal concerns.  When I got started most cross cutting concerns would have been solved through a mix of physical layers and common distribution models (shared libraries, jars, etc).  Today there are may more options thanks to PaaS and SaaS modalities.  At the same time, with the introduction of these options it has become far less clear what behaviors we should centralize and which of these we should replicate.  Where I find teams struggle is attempting to weed through *what* defines an appropriate layer to build.  For example, should we ask every software team to deploy their own Relational Database or should we create a central team to manage all databases?

What I am finding more frequently is that when I dig in with teams I tend to find that we are attempting to apply the DDD approach (ownership of a slice) but failing to consider some other importance considerations that can help us discern the need and want to take a dependency on other layers.  Where this becomes more complicated is when those layers might not yet exist or something that does exist won't quite yet fit your needs.  Do you take a dependency and wait or do you just go off and build your own?

I don't have all the solutions here just yet but one approach that I am liking more and more is to think about a style of Gradient-Based Decomposition where we focus on decomposing a system into sub-components based on the rate of change of a given piece of logic.

With Gradient-Based Decomposition we can ask ourselves if the thing we are building is something that we will likely modify over time in order to add value to the problem that you are attempting to solve.  If the answer is yes then we consider owning and deploying it ourselves.  If the answer is no then likely that component does NOT add value directly to the problem you are attempting to solve in which case we should attempt to find someone else to own it for us.  If you are a manager or product owner think about it this way: engineers are one of our most precious resources - would you rather that they maintain (upgrade, secure, manage) pieces of software that don't directly provide value to a customer or would you rather find a means of making more capacity for the team to work on additional behaviors that customers will find delightful?  I have had some success using this method to help identify common concerns that I see teams solving for and working to identify a more discrete set of common problems worth solving for (focusing in on the value of an abstraction).  As I spoke about in my first post about [Influencing Change](<{{ relref InfluenceChangePart1 }}>) it is important to focus in on the problem statement to be solved and not on a solution we would like to deploy.

**Focus on interactions**

Once we have defined how we might like to break up our software it is important to think about the impacts of how these components interact with each other.  In fact I would argue that this is the most important part of this entire post.  To this end I like to steal a term from financial markets - **Volatility**.  

This is where I feel that many of the tools at our disposal fail us.  They make any up front decisions too easy to break over time.  What is important is consider is how we can code and protect ourselves over an extended period of time by encapsulating and codifying any boundaries we have defined.  By focusing on codifying these boundaries we can hold accountable teams that deliver a capability to us.  This is where IaaS and Micro-services have had the upper hand over some other styles of development as they have forced a discussion around shared nothing architectures.  This is becoming easier to codify in other OOP languages as well (for example Java introduced Jigsaw).

My parting thought for this section is to makes sure we don't just focus on an output like unit-test coverage or cyclic complexity.  Those are good signals for sure - but take some time to tie those back to the Volatility in how a piece of functionality is delivered.  If you are delivering on a shared piece of logic think about how you can ensure a customer never experiences downtime and what is important in building an interface that helps you make that guarantee.

And finally, if you take a dependency on a component think about how you protect yourself should that dependency go down so that YOUR customer sees no impact.

Some books on my self related to this topic:

{{< rawhtml >}}

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=as_ss_li_til&ad_type=product_link&tracking_id=cstheorist-20&language=en_US&marketplace=amazon&region=US&placement=0321125215&asins=0321125215&linkId=412b7bec3bbe4c2aa8917e51a2ae7036&show_border=true&link_opens_in_new_window=true"></iframe>

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=as_ss_li_til&ad_type=product_link&tracking_id=cstheorist-20&language=en_US&marketplace=amazon&region=US&placement=B00YF0OSHC&asins=B00YF0OSHC&linkId=0b1edf3b85d423ee7b08ca82eaa24327&show_border=true&link_opens_in_new_window=true"></iframe>

{{< /rawhtml>}}

Finally - one book I discovered but have not yet read myself.  It introduces the notion of "Volatility-Based Decomposition" which I shall have to read up on...

{{< rawhtml>}}

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=as_ss_li_til&ad_type=product_link&tracking_id=cstheorist-20&language=en_US&marketplace=amazon&region=US&placement=B0822XJZ48&asins=B0822XJZ48&linkId=7a2fa179cf8a2eba6124253594dc1b12&show_border=true&link_opens_in_new_window=true"></iframe>

{{< /rawhtml>}}