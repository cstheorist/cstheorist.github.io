---
title: "Keeping Systems Decoupled"
date: 2020-05-20T07:49:43-04:00
readingtime: 1
draft: false
---
A common problem I have been asked to solve on a variety of the systems I have worked on is how to move data between independent components (different monoliths, micro-services, databases, etc).  My involvement occurred after many of these systems had grown organically as their respective businesses grew and expanded.  The result was that to move data between components many point-2-point solutions were created.  When integrating 2 components for the first time this makes sense but as the number of systems we attempt to connect together grows it is important to watch for some signs that things are turning into a "Big Ball of Mud."  There are some early warning signs (behaviors) that this is happening:

- If a new data consumer comes along and the reaction is more often then not to build a new asynchronous data stream for the use case.  It is an even stronger smell when a data stream already exists that contains the data (the contents of the data stream do not need to be modified) but isn't used.  There can be many reasons cited for this but let's consider a few and why this implies coupling.
  - Example 1: we hear that the change needs to be communicated to consumers (or more generally that there is concern existing consumers will be impacted).  When this holds true the producer is explicitly coupled to the consumer of that data stream.  This may occur because of technology choices that were made.  For example, if a producers pumps data onto a SQS queue multiple consumers may struggle to "share" that single queue.
  - Example 2: the new customer *requires* a different medium.  If we comply with this request it implies that we are leaking information about the internal protocol(s) that the consumer is using.  The consequence is that over time the team who owns the producing side must maintain and do the work to support each consumer prefers.  Unfortunately there are an infinite number of ways information can be moved so over time the burden of many data producers each having to support many consumers can quickly become an expensive tax.
- If a new data consumer comes along and we cannot educate them (point them at our documentation so they can self service their request) on the existing data stream that exists.  While this may be due to lack of documentation in practice I have observed it more frequently occurs because of unstated implications about the interface which have leaked details about the producer and the existing consumer(s).  In other words; I have found that many engineers are able to look at code and try to make something work but when this cannot be done quickly then we likely have not built the proper interface.
- If a new consumer comes along and we ask them NOT to consume an existing stream.

I have heard plenty of discussion that creating point-2-point solutions are more optimal (and I suspect that some reading this.  The question I always ask myself when faced with this though is how have we defined optimality?  Is it based on the speed at which bits can move?  Is it based on the ability to deliver a solution to market?  Is it based on the "Keep the Lights On" maintenance cost?   As a reader you may have heard the phrase "beginners mind" tossed around and that phrase applies here.  What is important over time is to refresh the set of Architectural Qualities (Non-Functional Requirements) that we are attempting to optimize for (at some point in the not to distant future I will post something on setting up a Fitness Function (writing down tenets) to track this).

Let us assume that at this point we have observed some form of coupling or that we would like to avoid some of the smells described above.  First; as engineers we should appreciate everything that has been built to date as it was likely an excellent choice at the time.  The question now is what is important to consider in keeping systems decoupled?  Here are some characteristics of a decoupled system:

* When a producer wants to expose a dataset to a customer they declare the set of information and the rate at which they intend to produce that information.  
* As new consumers appear there is no implication to the other existing consumers.
* In an idealized world as the numbers of consumers grow there is no impact to the producers ability to perform any of it's other tasks.
* While producers set the rate at which they produce, consumers may consume at any rate they desire based on their own independent design.
* As the producer needs to modify the contents of data sent consumers do not need to change in lock step (the data within the stream remains backwards compatible).

With these characteristics in mind lets consider three common solutions and how we might select a solution.

1. Time Series Event Streams
2. Message Queues
3. Micro-Batching

#### Step 1: Frame the problem you are trying to solve

The first step is to take the time to write down the problem you are attempting to solve.  This may sound obvious but I cannot tell you how many times I have seen teams start with a solution in mind (such as we will use Kafka) without first understanding the problem at hand.  As we will see shortly the cost of different solutions can vary widely (both in terms monetary costs as well and engineering costs and complexity).

#### Step 2: Define your Functional and Non-Functional requirements

Once we document the problem (WHY we are approaching the design to begin with) the next step is to make sure we take the time to align on both the Functional (what we should build) and Non-Functional (how we should build) requirements.  Like the problem statement skipping this step can lead to miscommunication.  For example, in my work on Robotics at Amazon it was not uncommon for someone to state: "we need the information in real-time."  If I went around a room of engineers and asked "What does real-time mean" I would receive different answers from almost each engineer.  In fact I will tell you from my work at Raytheon on missiles and Large Robotic Safety Systems the term "Real-Time" can have life or death consequences and has little to do with implying some notion of fast "speed" at which information is moved around.  Again; being specific about the requirements at this point can quickly prune many of the choices that may follow.

#### Step 3: Select the data movement pattern

Of the three patterns discussed there are some real cost implications.  Naively we can look at the price difference between the AWS offerings for Kinesis, SNS+SQS, and S3.  For the moment assume that we want to produce information so that consumers can observe a discrete set of events within 5 minutes of the event occurring.  In addition let us assume that events average over that 5 minute period 100 events/second with a size of 35KB.  

##### Kinesis (Time Series Event Streams)

For Kinesis the total data input rate is 3.4MB/sec (100 records/sec*35KB/record divided by 1024 = 3.4 MB/sec).

We then calculate our monthly Kinesis Data Streams costs using Kinesis Data Streams pricing in the US-East Region:

- **Shard Hour**: One shard costs $0.015 per hour, or $0.36 per day ($0.015*24). Our stream has four shards so that it costs $1.44 per day ($0.36*4). For a month with 31 days, our monthly Shard Hour cost is $44.64 ($1.44*31).
- **PUT Payload Unit (25KB)**: As our record is 35KB, each record contains two PUT Payload Units. Our data producers put 100 records or 200 PUT Payload Units per second in aggregate. That is 267,840,000 records or 535,680,000 PUT Payload Units per month. As one million PUT Payload Units cost $0.014, our monthly PUT Payload Units cost is $7.50 ($0.014*535.68).

Finally; to achieve decoupling and scale so that each consumer is independent we need to consider the cost of fan-out.  For Kinesis this is $0.015 per shard hour per consumer.  In the example above we had 4 shards so on a monthly basis this comes out to $44.64 per consumer.  In addition we must pay for the data transferred by each consumer which is charged at a rate of $0.013 per GB.  In the example above we produced data at a rate of 3.4 MB/sec.  On a monthly basis this would be 3.4 MB/sec * 60 second/min * 60 min/hour * 24 hour/day * 31 days/month.  Divided by 1024 this comes out to 8,893 GB * $0.013 = $115.61 per month.

So in total our monthly cost is $44.64 + $7.50 + $44.64 + $115.61 = $212.39 for one consumer and incrementally $160.25 for each additional consumer assuming all data produced is consumed.

##### SNS + SQS (Message Queues)

To achieve a similar system using SNS and SQS we need to consider again the producer and consumer costs.  To simplify this example we will consider sending each even independently (some readers may recognize that multiple messages could be packed into a single request we see that requests are charged by 64 KB chunks so there is some potential cost savings). 

SNS is charged at $0.50 per 1 million requests.  At 100 records per second this results in 257,840,000 records as well as 257,840,000 SNS requests.  On a monthly basis this costs $128.92.

On the consumer end we need to pay for the SQS requests.  These are charged at $0.40 per million requests.  This results in $103.13 per consumer.

So, for a single consumer our cost is $232.05 and each incremental consumer is $103.13.  Compared to the previous solution at scale the dominating factor is the incremental consumer cost which is about a 33% savings.

##### S3 (Micro-Batching)

For some reason I tend not to hear many people talk about Micro-Batching but it is actually one of my favorite patterns for moving data around when thinking about building decoupled systems.  We can think of micro-batching as the process of producing small files at a set rate (period).  For our example we wanted to make sure consumers could see data within 5 minutes of production.  To achieve this let us simplify our lives and presume we will produce data at a rate of 1 file per minute.  Each file would be approximately 204 MB in size and over the course of a month we would produce 60 * 24 * 31 = 44,640 files and 8,894 GB of data.

There are 3 cost components with S3: Storage, Requests, Data Transfer.

Storage is charged at $0.023 per GB.  If we assume we keep 7 days of data we would be charge $46.19 / month for storage.  

Requests are charged at $0.005 per 1000 requests.  We will need to PUT 44,640 files and let us assume we run some number of LIST requests as well (at 10% of all PUTS).  This would lead to $0.25 per month.  In addition clients need to GET requests which are charged at $0.0004.  We will assume that clients attempt to retrieve twice per minute looking for the new files as they are produced.  This would lead to an incremental cost of $0.04 for each client.

Finally; we need to pay for data transfers.  There is no charge when storing data and when retrieving data if the clients are within AWS we are charged $0.01 per GB.  This equates to $88.94 per month.

In total this solution would cost $46.19 + $0.25 + $0.04 + $88.94 = $135.42 and each incremental consumer would cost $88.98 per month.  This is almost a 20% savings compared to SNS + SQS and almost half the cost of Kinesis.

One under-appreciated value of this specific pattern is that producers can also greatly reduce the cost for consumers in two common ways.  First when raw events are not required but rather aggregated data.  Second through introduction of a compression algorithm on the data produced such as GZip.  Combined it is not uncommon to reduce the data transfer cost by 50 to 90% (or more).

#### Conclusion

What I really want to make people aware of are these asynchronous forms of data movement but to also remind developers of the importance of designing clean and repeatable interfaces just like we would a REST API.  Once we have defined a pattern in which we can move data around PLEASE take the time to document the format of data that transits the wire, think about how you will version these "documents", and consider simple solutions that are easy for your customer to reason about.

I have plenty of additional thoughts rolling around having written this down: #MicroBatching, #BackwardsCompatibility #FitnessFunctions

Books on my shelf related to this topic:

{{< rawhtml >}}
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=as_ss_li_til&ad_type=product_link&tracking_id=cstheorist-20&language=en_US&marketplace=amazon&region=US&placement=B00794TAUG&asins=B00794TAUG&linkId=deb7c2da0e733948bd2334616b41ef75&show_border=true&link_opens_in_new_window=true"></iframe>
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=as_ss_li_til&ad_type=product_link&tracking_id=cstheorist-20&language=en_US&marketplace=amazon&region=US&placement=B00T3N7XB4&asins=B00T3N7XB4&linkId=f627f6ecb3ebfc61e10e043cee2ad483&show_border=true&link_opens_in_new_window=true"></iframe>
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ss&ref=as_ss_li_til&ad_type=product_link&tracking_id=cstheorist-20&language=en_US&marketplace=amazon&region=US&placement=B01LZQGIIC&asins=B01LZQGIIC&linkId=447a9ff5d6ddefbf0db0d500c22057ff&show_border=true&link_opens_in_new_window=true"></iframe>
{{< /rawhtml>}}

