---
title: "Maintaining our Technology Garden"
date: 2020-06-21T07:22:59-04:00
readingtime: 1
draft: false
---

I have worked in organizations of many different shapes and sizes.  Some as small as just myself, in startups including tens of engineers and still others that have included tens of thousands of engineers.  When organizations are small it is fairly easy to keep track of all of the pieces of technology used.  This is generally driven out of both interest and need (a team of 10 engineers would struggle to learn and develop on dozens of different databases, multiple build and deployment systems, and many different programming languages).  Still, as organizations grow it is not uncommon to find that the once streamlined technology stack begins to include more and more moving pieces.  Is this ok?  When should we stop it?  How do we encourage innovation and exploration without every team having to manage all things?

**Gardening**

Growing up I had parents and grandparents interested in Gardening.  Perhaps the wore off on me a bit.  Putting together a garden requires thought and planning.  You may start with a single flower that you really enjoy.  As your garden continues to grow you may want to start adding in other types of flowers as well.  As your garden becomes more diverse it is important to start to think about what you already planted - otherwise you might not be able to tell weed from flower and suddenly your beautiful garden becomes overrun.  I like this metaphor to consider when we think about tending to our technology garden.  We want to try new things but we also want to watch for what technologies we are leveraging as to ensure our garden remains beautiful.  It demonstrates a balance that is important to strike that allows for growth while not allowing things to get out of control.

**Tending to our technology stack**

I like to write down tenets when thinking about how to manage this balance because much like problem statements I find that if I can get clarity and agreement on a few tenets then they in turn can be leveraged when making technology choices.  For purposes of selecting or reviewing a technology portfolio I like the following:

* Be IP Driven and focus on developing solutions that benefit customers.  My belief here is that we should invest engineers time towards solving problems (adding value) for their customers.  The more time spent solving problems that are not directly related to the business (or part of a business that a team is associated with) implies the slower (or more expensive) solutions for customers will be.
* Be Standard.  Follow paved paths and deviate only when the business needs.  In other words, it is helpful to not deviate from paved paths every time an engineer reads about a new technology, rather, when building a new path we should be able to describe the value that it adds to our business.
* Be Ready To Question.  While existing paths exist what made those good paths before might no longer be the case.  Looking for new paths can allow us to reduce existing engineering efforts and open new opportunities.

Generally I like to think of the above in relative order but the importance is less about those that I have placed in this document and more about taking the time in an organization to work on gaining agreement on the most important driving criteria.  For example, my set of tenants working on missile systems at Raytheon were quite different from those when I joined the robotics startup Kiva Systems.

Once we have a set of tenets the next question is how to structure the process of making decisions.  For this I like to apply a rule of *Conscious Deviation*.  The idea behind the rule is straightforward:

* Write down the problem you are trying to solve for.
* Look to see if a paved path exists that can solve the problem.  If one does then prefer that path.
* If a paved path does not exist then perform a technology selection and write down your decision (create a new path).

By following this rule we are explicit about making choices.  In other words, we are conscious about what technologies we choose.  We encourage everyone to participate in helping shape the technology portfolio and looking out for what is best for their part of the business.

**Setting up a technology selection process**

While the rule of conscious deviation is straightforward it is just a rule and doesn't tell us the process for applying that rule.  I am generally a big believer in minimizing formal processes but this is one area where I have been burned by not looking aggressively enough and thus have perhaps allowed a few more weeds then were necessary to pop up.  As a result over time I will admit that I have slowly iterated towards the belief that for conscious deviation to be successful there needs to be at a minimum some level of cooperative review of technology choices being made.  Now this does not imply a long process by which a select few in an entire company must sign off on each decision, but rather a process should exist that at a minimum ensures choices made are transparent and discoverable.  Doing so allows the senior technologists in an organization keep track of the choices being made and being held accountable for those choices remaining consistent with the objectives of the business.  It also ensures those same senior technologists can work together to plan out the garden (technology roadmap) as organizations grow.

*Note: I often see teams write things down and one can argue the choice is transparent - but you would need to know where every team has written such things down.  The discoverable component here is what I find lacking most frequently.  I want to be able to quickly find the set of choices that have been made as to assess the overall health.*

Depending on the starting point that a given organization finds itself in it may be appropriate for adding some additional checks along the way.  For example, in my opinion it IS appropriate when an organization finds that things have gotten out of hand to be explicit in defining a more specific set of technologies be leveraged.  In organizations that have a strong DevOps and Service Ownership culture when this happens it is even more important that the explicit problem being solved for be written down and expectations clearly communicated as such directives remove a choice from a team (a team was not autonomous) and Autonomy, Accountability, and Ownership are all connected (check back soon for a post on this...).

**More then just building the new thing**

While most reading this are likely thinking about the implications when building a new project it is also important to consider the fact that many technology choices live longer then the business objective that led to the initial choice.  For example, if a company pivots into a different customer segment or if a company is acquired or simply as a company grows to serve more clients then originally projected.  In my career I have argued to change database technologies, consolidate engineering efforts from many teams solving similar problems to a single team, deprecate business functionality thought to be vital, align different organizations within a single company build a singular technology roadmap to solve an even larger customer problem, and more.  In most of these cases there has been significant investment (time, energy, learning) to date of many engineers on what exists which creates significant friction to moving towards something different.  Stated directly, the near term incentives are stacked against such efforts:

* Engineers may not see the value is giving up something that they have built.
* Product Owners may not realize an immediate customer value.
* GM's of a large organization will see such efforts as a tax.

What is important is that this should not distract us from working to make data driven assessments to articulate to the business when the time is right to invest in changing fundamental components of a technology stack.  While the same rule can apply in re-evaluating existing technology choices I have learned the one or more of the following can be helpful in gaining adoption:

* Write down the timeframe for a return on the investment.  Many engineering leaders are comfortable looking further down the road and it helps when investing in technology can be framed in a way similar to making an argument for say making an acquisition of a company or starting a new line of business.  In other words, while everyone is focused on the near term cost we want to carefully articulate the long term value of the investment (much like we might think of compounding interest in a financial market).  
* Be willing to start small.  Not all projects must be accomplished all at once.  For example, let's assume an organization has grown organically and through acquisitions and has a mix of many different RDBMS solutions (mysql, postgres, SQLServer, Oracle, etc).  Can we remove just one of these from the mix?  Or can we work with one team where the benefit may have the greatest impact to their team velocity?  By starting small it is possible to collect information that can be used to build a case for a larger organizational investment.
* Celebrate deprecation of technology.  One way to shape the minds of engineers is to celebrate whenever technologies are deprecated.  Look at the toil that is saved or the amount of code that no longer needs to be tested.  While engineers often like to build new things having a culture of engineers wanting to remove "old" things helps to keep everyone on the lookup for such opportunities.
* Normalize terminology.  This may not seem obvious but the bigger the audience for a change is the more important it is for everyone to be speaking the same language. 
* Be concise and make no assumptions.  Like normalizing on terminology the bigger the audience the fewer assumptions should be made about what  is driving the decisions of the audience.  It is important to be articulate and concise about what is driving a particular decision.  Adding too much ancillary information will distract from the actual drivers of the decision and likewise omitting an important factor can undercut an argument because it assumes everyone understood the specific pain point or need.  

**Final Thoughts**

I would love to know what has worked for others in tending to their gardens.  Where have *you* looked to more senior engineers to provide guidance or when have you wished for more flexibility?  Do you have a simple rule like *conscious deviation* that has worked to communicate such intents to an organization?  If so post it!