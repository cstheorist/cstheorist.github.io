---
title: "Learning in layers"
date: 2020-05-24T07:22:59-04:00
readingtime: 1
draft: false
---

While back in school I spent time studying the human visual system and worked on attempting to model that system in software through the creation of various Neural Networks.  The approach I took was to model various layers in the human visual system (LGN, V1, MT, etc).  The results I obtained at the time were interesting in observation but not practical in practice for a number of reasons (computation was to slow, lack of large training data sets, etc).  In the last 20 years a lot has changed so it got me wondering if any of those concepts or approaches had since been applied.  More specifically, if current models of detecting objects attempted to take a layered approach.  If I were to look at the problem with fresh eyes today I might attempt something like the following:

* Create a model that aims to detect lines and angles.
* Create a model that takes the previous layer as input and looks for primitive objects.
* Build up from primitive objects to complicated forms.
* From complicated forms we can track time and learn types of interaction.

So, let us say that we want to create a model that looks for people in an image I might first look to reduce the raw pixels into basic shapes, then look to learn from those shapes what might be a "head", "arm", "leg", etc.  Then from there I could look for what are the most likely humans.  Finally, we can look at if 2 people are interacting with each other (talking, touching, moving out of each others way, etc).

I would posit that an approach like this might work well where discrete detection of individuals is important (tracking a person in a crowd, tracking people interacting with each other, collaborative robotics, etc).  By building up from pieces I would invert the need to perform, for example, pose estimation given what I presume is a body and instead would derive a body from potential poses that I have observed.

There are plenty of problems that I believe that such systems could solve where we would want to track individuals in crowds and/or through occlusions (store automation, security tracking, OSHA Compliance, etc).  I wonder if such a method would be more robust to different and changing environments then models that attempt to simply identify people and then attempt to estimate pose and behavior after the fact..

If anyone has written or seen any papers in this space please share as I am curious what we might consider state of the art in this space today!

