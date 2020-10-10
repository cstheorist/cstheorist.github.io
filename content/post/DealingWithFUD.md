---
title: "Dealing with FUD and making the tough choices"
date: 2020-06-06T07:22:59-04:00
readingtime: 1
draft: false
---

Getting large changes rolled out within an organization or with customers is often not easy.  One particular set of changes are those that fundamentally change the way something that is already deployed functions.  We might like about these projects to changing the wheel on a moving car or the engine on a flying plane.  After a lot of really solid engineering work is complete and demos are done, actually deploying the change seems like an impossible task.  Each time we think we are ready to take another step forward we find yet another obstacle put in our way (one more test case to consider, one more potential consumer that might be impacted, etc).  Over the years I have found that Fear, Uncertainty, and Doubt (FUD) are some of the biggest reason that such projects stall.  So, are there any techniques that lead to more success at getting these projects over that last 10%?    

**Step 1: Recognize the FUD is natural**

In life there are many things where FUD creeps in.  If you have ever experienced Impostors Syndrome you have encountered FUD.  For large projects when we are getting ready to deploy it is much the same.  We might fear of a bug breaking a customer, we may be uncertain if we have done enough testing to get to the point we are, we may have doubt that we are solving the right problem for the customer.  All of this is natural and I would suggest that if you get to a point where there is no FUD when releasing software then you have been to cautious (note: I have worked in safety certified systems so presume release for this context is the first deployment to some formal, often external, validation exercise).

**Step 2: Recognize that failure WILL occur**

The reality is that we don't live in a world where all things can be predicted.  The implication is that some percentage of things we, as humans, build WILL fail if for no other reason then we did not foresee some force (or characteristic of force) acting upon the system.  This is simply fact (until some day that we have a model of the universe and all thing ... something something - 42).  

**Step 3: Be transparent about, listen for, and write down risk**

If we agree that failure will occur then we need to learn to embrace failure.  What is important to recognize about FUD is that it is in fact one of the best opportunities to listen and learn how others think about parts of the problem being solved.  It may be that someone brings a particular customer point of view, was burned by a similar style change, or simply has never seen a change like the one being proposed completed before.  When people are talking it is important to listen.  Do not shy away from what people are saying but lean in and acknowledge the FUD in the room - everyone appreciates that they concerns have been acknowledged.  Going through this process can help everyone tease out general discomfort vs inherent issue that requires resolving.

**Step 4: Embrace that Failure will occur - Optimize to resolve any failure**

I often hear a lot about finding things "earlier" but I actually am not sure I completely agree.  Sure, I would love to find problems "early" but I would rather focus my energy on two other measures:

* minimizing the time between a problem being introduced and the time we resolve that problem.
* minimizing the impact when a problem does arrive.

Solving to minimize these can take many different forms but the focus on globally optimizing for these can look different in various organizations.  I have seen companies that have fully embraced a fail fast in production style culture - I would be hard pressed to ask those teams to focus on more up front process and design reviews if any change can be rolled back in seconds.  I spent a number of years at Amazon and something that made Amazon very successful at this was their focus on automating everything.  Instead of focusing on more process there was a strong focus on automation (automated alerts, automated deployments, automated rollbacks, automated everything).  You may be the word "NoOps" which I personally like to think of in the vein of getting really good at automation such that the system is robust not just in its primary function but in its ability to rollback to a previous function should something go wrong.

If we think about the outcome we are attempting to achieve in this way we can begin to look at the fundamental architecture of the systems we build as well.  We can start to measure how different design patterns lend themselves to such optimizations and do not just need to focus on shifting our processes left.

**Step 5: Make the call**

One of the harder part of getting a change rolled out is simply the willingness to make the call to move forward.  For large enough changes leaders need to provide their teams the confidence that now is the right time.  Hopefully if everyone feels they have been heard then this is the point that whomever owns the decision simply needs to make a call.  I don't know of a one size fits all answer to this as different projects and organizations have a different tolerance for risk.  What is important is driving clarity as to what is an acceptable level of risk.  A tip I have found useful here is that for some projects it can help to contrast pushing out a change with the risk of taking no action.  It is interesting just how frequently:

* for changes to an existing system the change, even with some amount of risk, is likely to lead to a better outcome then continuing to live with the thing that may already be running in production.
* for some net new feature - how exposing the feature can only lead to a potential net positive for a customer as they begin to use it.
* for a fundamental change in how a product functions how doing nothing will only allow competitors to catch and and pass you.

**Step 6: Continuously Learn**

When FUD is realized a problem occurred and it is time to learn.  That said; when bugs make it to production no one wants to be outed for directly introducing the problem.  I would go further and say - no engineer should be forced to be outed in such a way either.  This is where I often need to look at myself (or other leaders in an organization) to see how they react in order to set the stage for learning.  If you are a leader then when a problem has occurred it is important to acknowledge that we (you) took a measured risk.  As the engineering leader this is a great way of demonstrating how you hold yourself accountable.  Again, failure is inevitable and so, assuming you knew as a leader that there was risk, it needs to be ok to stand up and say - "The failure is on me - I knew that there were risks and believed now was the time to push forward".  Having been an engineer on the other side having introduced my own fair share of bugs into code I will say there is nothing more re-assuring then to hear a leader dis-arm a situation and put my own mind at ease.  No engineer wants to be known for introducing a bug.

With everyone's mind at ease the focus can then, and only then, shift to what we can learn about the style of failure.  Here again I often like to move beyond the specific problem that arose (hopefully at this point a fix was produced so the acute problem is understood and known).  What is important now is to look at the style of issue and determine what potential actions are worth taking.  Keep in mind I am not talking about some form of retro for any small issue, but rather think of the big failures (like wiping out a database with no backup in production - not that I ever did that?).  

I like to encourage everyone taking part to not think about the specific code issue but again think about what steps would have improved the optimization we talked about in Step 4.  I have seen far to many times that a new metric or alert is put in place that is extremely narrow in scope to catch exactly the issue that just occurred.  That leads to a game of wac-a-mole.  So now is the chance to think about the broader customer indicators that might identify a broader set of problems (including this one) earlier or a change in the automated rollout strategy that would allow for smaller impact, or a change in the deployment architecture that could allow for faster recovery.