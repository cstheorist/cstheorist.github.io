---
title: "A Strategy and Proposal for Assessing Cloud Service Readiness"
date: 2020-10-11T07:22:59-04:00
readingtime: 1
draft: false
---

Throughout my career I have been held accountable for the maturity and quality of code deployed into production.  When I was first coming out of school this was fairly easy to assess (did the code I write ever cause a problem and when it did learn from it).  As I grew as an engineer playing combinations of Management and Lead Engineer styles of roles I started to work with my team(s) to ensure we were on top of code reviews, looked at our testing procedures and more.  As my scope increased I eventually help titles like "architect" and I started to realize that by the time code was being written large problems may have already been baked into the design (perhaps you have heard the term "one-way door").  It is around this time that I started to think about a more holistic set of criteria that needed to be thought about.  I started to bake criteria into templates such as a design document that provided a template of things to think about (I like to use the term non-functional requirements because these are the things the team has decided are important in HOW they will approach and build the service and given those choices these are requirements that themselves must be met).  Over time I further realized that simply writing things down into a design document was often insufficient because by the time a service was ready to be deployed that document may itself have become stale.  My solution back then... the creation of more templates (service readiness template for example) which could be filled in at different points during development.  I will also say I probably didn't make many friends of engineers (or managers) at this time as the consequence of all of my templates was the need to fill many things in and often time the content was duplicative - but getting teams to take the time to think of quality in this way did lead to desired outcomes and improved overall success of software (and systems) that were being deployed.  There were two gaps that I started to identify at this time however:

* Everyone started to think in terms of maturity models.  The danger here is that everyone wanted standard groups of written requirements that were prioritized.  While I get the appeal the problem is that it misses the point that the intent is to understand controls (those requirements) and the associated risk.
* The templates I created led to thinking of the assessments being "gates" that teams would pass through.  While this was great when a service was first built it didn't account for the reality that we would both learn new things over time that should also be considered AND with enough changes over time things that may have been thought of previously may no longer hold true.

This has led to my more current thinking which is to think of Service Readiness a continuous process where both the requirements AND the assessment against those requirements can always be considered.  The question then is what can (or should) this look like.  The remainder of this document focuses on:

* providing a discrete example of what I mean by a Service Readiness Assessment (and stress the intent of identifying risk)
* asking the question - should there be an open source version of this and would this provide broader benefit to our industry?

**Assessing Service Readiness**

First, I want to state that for purposes of this document a service is any piece of code that is independently built and delivered so that other teams can use it.  I don't presume that this only applies to micro-services as I have found this thinking can apply to monoliths as well (especially what I defined as [Modular Monoliths](https://theorize.co/post/whatisamonolith/).

So what does a Service Readiness Assessment look like?  There are 3 components that make up the assessment:

* Definitions
  * What I have found helpful here is to provide definitions that provide service owners to determine the appropriate context for their review.  Effectively the definitions should provide enough information so that a reviewer can best determine at a high level what requirements are most relevant and for all stakeholders to understand the risk that a given service has on the business.
  * An example of a definition would be to create a set of Service Tiers (Risk Tiers).  I personally prefer, for example, a simple tiering system such as Tier 0 services being those that can take impact a customers production deployment and Tier 1 being everything else.  
  * What is important with any definition is that they are clear so that teams can properly apply the definition(s) to the service that is under assessment.
* Service Controls (Requirements)
  * There are a lot of examples of production readiness checklists that can be pulled from.  [Example 1](https://www.oreilly.com/library/view/production-ready-microservices/9781491965962/app01.html)  [Example 2](https://gruntwork.io/devops-checklist/)  [Example 3](https://jbd.dev/prod-readiness/)
  * I tend to think of groups of controls as they relate to architectural '-ilities'. My reasoning for this is that when a team needs to determine their risk I often aim to ask what are those architecture '-ilities' that contribute the most risk.  Sometimes an organization may broadly apply these (Availability, Testability, Observability, etc) but depending on the service is may actually be something like "Durability" that becomes exceedingly important.
  * For each control that is defined there should be a statement as to which Definitions that control applies.  For example:
    * Service Owners MUST issue alerts when it approaches a defined SLI and whenever an SLA is tripped.  Applies: Tier 0, Tier 1, ...
    * Service Owners MUST have a 24x7 Support Plan with a defined SLA for response.  Applies: Tier 0
  * What is important about Controls is that they focus on WHAT should be done and possible explain WHY.  They should avoid HOW things are implemented (imagine wanting to quickly assess a potential acquisition or services post an acquisition - if the controls are specific to HOW things are done then we will defeat understanding the risk and become focused on implementation details).
* Assessment Template
  * In an ideal world there would be a tool that could track assessments over time and in one place that could also provide outputs (links) that can be referenced from other places.  I noticed recently that Eric Evans may be cooking something up in this regard.
  * I personally like simple templates that ask Yes/No questions and then ask for a link to evidence.  
  * An alternative is to let teams rate themselves (for example from 1 to 5) but this comes with a few big cautions:
    * Services cannot be compared directly.  This is because, like Story Points for teams doing scrum, such scores are relative to those who are performing the assessment (I have found some of the most mature teams are the most critical of what they have built).
    * If a control isn't fully mitigated then by definition there IS risk and this can lead to significant debates which can distract from the intent.

OK - so with these three building blocks what happens next?  This will depend a bit on the culture of the organization but I have found that asking teams to revisit such an assessment yearly and any time there is a large outage (or frequent smaller outages) to be a good starting point.  The yearly timeframe ensures that new controls are caught and assessed over time.  The ad hoc assessments often ensure that other processes (such as a retrospective) look over previously documented (known) risks and enables an open discussion should such risks still be accepted.

**Open Source and Standards accepted approach**

While I have seen a lot of examples (as linked in the previous exception or just google for many more) there seems to be a lost opportunity both in how companies can advocate for the maturity of their offerings as well as to learn from each other.  IaaS, PaaS, SaaS, along with many other aaS style offerings are becoming ever more important and yet outages (think about the recent Microsoft 360 and Teams outage, Cloudflare outages, Salesforce outage in 2019, and many many more) still occur.  As companies rely on such services and as their own offerings become more critical to those companies own success or failure simply looking at past performance is no longer enough.

One way of looking at this is that there has been movement in other critical areas such as Cyber and Information Security where the NIST CNF has taken on a larger role (as well as Standards such as ISO/IEC 27001).  Now seems like the time to build this concept out further into a framework that leads to an overall industry improvement in Availability, Observability, Recoverability, Durability, etc. and helping companies better understand their overall risk profile in order to best serve their customers.  I posit further that creating a large discussion in this area can more broadly educate the users of aaS offerings as to the importance of engineers investing investing in these areas which are often overlooked until there is a problem.

I would love to see if there are others that think like me.  Are others aware of efforts that may exist already?  And if not please feel free to send me a note (I may build out a discord server should there be enough broader interest in building something out).