---
title: "Are you there? More than just availability..."
date: 2020-05-28T07:22:59-04:00
readingtime: 1
draft: false
---

Technology is constantly changing all around us.  Could you imagine making a phone call today and missing every other word?  What is the voice of the individual on the other end was delayed by 10 seconds?  What is the sound was muffled?  I suspect most of us would not find those conditions unacceptable.  Now go back 20 years - how might we have felt then (remember those great analog based cell phones)?  Technology is constantly progressing and as engineers we need to look out for where it is moving to continue to move the industry forward.

For me one of those key areas of change that is specifically worth calling out today is Availability.  For a long time availability meant dial tone.  Think back to the days of dial-up (I like to imagine that most engineers still remember those days of beeps).  I would consider something available (prodigy, aol.com, etc) as available if when a request was made some response was returned (eventually).  Beyond dial-up came DSL, and Cable, and Fiber connections.  Generally everyone came to expect not just a response but a response in a reasonable timeframe.  Amazon and Google began to measure user behaviors and responses became slower.  Then mobile phones became a go to device to stay connected.  Mobile connections were not as predictable and some expectations were reset to be a bit more flexible.  Once again though in the past few years LTE connections have raised expectations and I largely expect the 5G will force a continued customers behaviors to be driven towards fast responses.

Finally, and Machine Learning improves customers will expect those experiences to not just be fast but also tailored to their personal preferences.  This implies not just being able to respond quickly but to also incorporate new information quickly.  For example if I go to an e-commerce website and am browsing shoes and then type the word "red" I expect to see red shoes not just red things.

So, my question to those reading this post; if I asked you if your service was available then what should the answer be?  Generally we tend to think of things in terms of SLAs, SLOs and SLIs.  For those who may not have heard those terms:

* A SLA (service level agreement) is an agreement between provider and client about measurable metrics like uptime, responsiveness, and responsibilities. 
* A SLO (service level objective) is an agreement within an SLA about a specific metric like uptime or response time. So, if the SLA is the formal agreement between you and your customer, SLOs are the individual promises you’re making to that customer.
* A SLI (service level indicator) measures compliance with an SLO (service level objective). So, for example, if your SLA specifies that your systems will be available 99.95% of the time, your SLO is likely 99.95% uptime and your SLI is the actual measurement of your uptime such as 99.96%.

In the context above availability was simply defined via the legacy notion of "dial-tone."  In other words, did we get some valid response when a request was made.  But now that technology has continued to mature is that enough?  Does dial-tone meet a customers expectations?  As engineers building software today perhaps we can work backwards to better consider what actions we should be taking now.  If there are areas we may want to offer an SLI the first thing we might consider is measuring those areas and focus in on the SLIs.  If we have decided that something is important to measure we should also take the time to write down what we think is a reasonable set of bounds for the thing we are measuring.  For example, if we are building and exposing APIs we might consider some of the following SLOs:

* Latency: 99% of calls return in under 1 second 
* Consistency: 95% or reads that occur after a write return with the value written
* Predictability: All calls with equivalent inputs return within +/- 5ms
* Rate: All calls up to 10,000 RPS are processed

By taking time to write these down we set ourselves on a path to also ensure we are measuring each of these objectives by measuring these qualities in production and monitoring our SLIs.  With this we can gain confidence, track how our processes (Deployment, Feature Rollout, CI Systems, Test Harnesses, etc.) can drive conformance and ultimately move to the step of SLA and making commitments to your customers.  As the inherent expectations continue to grow I believe that these additional SLAs will become one differentiator in the marketplace driven my clients (consumers) expectations.

A takeaway is that as technology improves the expectation on those technologies continues to go up as well.  As engineers we need to constantly remind ourselves of this fact and continuously keep an eye out as the world around us continues to progress.  Stay still and you will find your competitors move ahead.

As I have gotten to the end of this post I feel it is important to spend some time thinking about characteristics that lead to robust designs (dampen noise in inherently unstable environments).  Stay tuned as I suspect that will be what I take some time to ponder next.

