---
title: "Digital Doubles and Statefulness"
date: 2020-05-23T07:22:59-04:00
readingtime: 1
draft: true
---

Most of my life I have worked on systems that attempt to provide some model of the real world in digital form.  This might take the form of building a sensor that attempts to create a view or perspective of the world directly (RADAR, LIDAR, Thermal Camera, etc) or some higher level system that takes information from such sensors to build an aggregated view (air traffic control system, model of assets throughout a warehouse, etc).  This leads to the question: if I build a software system that models such a world just how much state (information) do we need to store?  How much of that information should we backup (how important is durability)?  Should we treat all information in the same way?  What are some important criteria for thinking about if and when information should be stored as state?

Let's set up a bit more context for the conversation to help us define some characteristics that may be useful to help us answer some of these questions.  Let us imagine that we are going to build a system to optimize traffic lights at the intersections within a cities bounds.  We setup a fitness function and measure success by looking to maximize the total volume of objects that make it through all intersections (ok - in the real world this might rather optimize for things like total number of people that transit an intersection but for purposes of keeping it simple...).  As inputs to our system we have a number of cameras setup on top of each traffic light and pointed towards the center of the intersection each light is associated with.  The question now - what information should we model, where should we maintain and persist state, 





There are plenty of other interesting examples here as well.  For example, we might consider a store full of products.  We obviously would like to track each and every



