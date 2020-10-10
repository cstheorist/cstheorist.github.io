---
title: "The Rule of 2AM"
date: 2020-05-26T06:22:59-04:00
readingtime: 1
draft: false
---

The rule of 2AM goes like this:

* As a developer when you are about to submit your pull request ask yourself, will everyone on my team be able to understand what the logic I have just implemented does at 2AM within 15 minutes?
* As a reviewer looking at code that you are about to approve do you believe those around you will understand it at 2AM within 15 minutes?

Pretty straight forward right?  

In practice this rule has served me well in a number of situations.  

* When I or someone on a team I am working with is looking to leverage some new library they heard about.  If you are a Java developer a library might be something like Lombok.  At first blush all seems harmless until someone on your team wakes up at 2AM, doesn't have a development environment that understands how to follow the annotations and that same poor developer comes to believe that fundamental pieces of code might be missing.  
* Sometimes a language is upgraded with a new feature (remember when lambda functions were first introduced to Java)?  2AM rolls around and someone who doesn't understand how a ForkJoinPool is confused as to how threading on that .parallel statement is functioning.  Or maybe they have a stack trace but can't determine where in the code it is failing because everything appears to be on the same line or leveraging anonymous classes.
* Perhaps someone wants to simply leverage a new language (python, Scala, Kotlin, etc).  In fact some may already be compatible with your runtime as is but come 2AM will others on your team understand how they are working together?
* How many comments are important?  To many comments are redundant but every now and then we might get a bit lazy.

The 2AM Rule is something I started talking to my peers about after seen multiple instances of all the above.  I needed a way of communicating that come 2AM it felt important that we could resolve a problem as quickly as possible but I also wanted to make sure we had the flexibility to introduce new tools over time.  By asking the simple question I found a way to walk this balance.

As long as a team was able to pass the test then likely we were safe to move forward but if not then there was some more work to be done prior to moving forward (be it training, adding more comments, creating more metrics for visibility, etc).

What do you think of this rule?