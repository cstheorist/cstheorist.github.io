---
title: "Little's Law - getting things size right"
date: 2020-05-31T07:22:59-04:00
readingtime: 1
draft: true
---

So you spun up your web application and start serving requests.  What happens as more and more clients make requests?  At some point requests start queuing; you need to scale up or out but how do you know how to grow?  And is there anything that can guide us so it is less of a guessing game?  The answer is yes; and it comes from the Mathematical Study of queueing theory.  

**Little's Law**

> The long-term average number of customers in a stable system of length N is equal to the long-term average effective arrival rate, λ, multiplied by the average time a customer spends in the system, W; or expressed algebraically: N = λW.

Little's law is universal and can be applied anywhere that a queue is present.  In our use case we can consider a client's request to be the entity to be processed.  These entities are either sitting in a queue or being processed.  Little's law 

Because we are talking about a stable system we presume that the arrival rate is equal to the exit rate of the system.  The length of the system is equal to the number of 