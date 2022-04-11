---
layout: post
title: "Use delta-based tickets"
date: 2022-04-06 23:40:56 +0100
comments: true
categories: 
---
           
There are two ways version control systems model changes over time: delta-based and snapshot-based. By exploring the distinction - and when to apply each - it's possible to explain many common problems with planning, tracking and communicating software development.  

<!--more-->

## Types of problems
                  
A common model of the software development process is roughly as follows:

* Requirements or specifications are generated
* These requirements are considered and refined
* Developers implement these requirements
* The developers' work is reviewed for code quality
* The work is confirmed to meet the requirements
* The work is released and observed, and the process repeats

This process often doesn't work perfectly, and it fails to account for the errors that may occur at each stage.

* The requirements may turn out to be wrong, incomplete, ambiguous or confusing. In a perfect system, this is always picked up and resolved at the start (or along the way). But things are rarely perfect. 
* Developers may perform the work in a sub-optimal fashion; ideally this would be picked up at the code review stage, but it doesn't always happen. 
* A developer may have partially implemented the requirements, or they may have introduced new bugs. 
* In changing some of the system, developers may have triggered the realisation that further work may be required, or more factors considered.

There are a few ways these imperfections are typically handled:

Tickets can be rejected, re-opened, or moved back to an earlier state, causing confusion around the status of the development branch or (bleeding) edge environment. Work marked as re-opened may in fact have already been applied (but with defects). 

Alternatively, tickets can be held in a particular state, forbidden from proceeding until some required further action is performed. The ticket still appears to be in a state that it really isn't. A long dialogue of back-and-forth in the ticket's comments may reveal the details of the status of the work, but it's slow to go through and discern this for every ticket. Work may be blocked for extended periods of time while minor kinks are worked out.
                          
This way of modelling development effectively applies a _fuzzy_ state: the work is _mostly_ done; exactly what is meant by _mostly_ is often frustrating to clarify, and it leads to misunderstandings.

## Version control types 

System behaviour is expressed in code. And code is typically managed in a control system that allows it to be versioned over time (i.e. developers can see previous versions if they so choose). 

There are two types of version control systems (for our purposes): 

* **Delta-based**: An original version of the code is recorded and then all subsequent changes are played "on top" of it in a sequence, until you arrive at the version you're interested in (the more times something has changed, the more changes that need to be replayed; and the more differences between consecutive versions, the bigger the changes that have to be applied)
* **Snapshot-based**: A stream of entire snapshots (versions) are saved, with a link to the previous one (the size of the differences is irrelevant - a whole new snapshot is created each time; you can, however, jump straight to the version you want without having to fast-forward from the very first one) 
                                                          
Changes between versions are expressed as _patches_ (a list of instructions to change atomic pieces of information to new values - e.g. lines in a file). In delta-based systems, the patches are the steps to get from one version to it's successor. In snapshot-based systems, patches can be derived by comparing two snapshots of the same file. That is to say, in delta-based systems, the patches _are_ the changes - they are active - whereas in snapshot-based systems, the change is the new version and patches are just a _description_ of what is now different - they are passive. 

The process of making code changes is _itself_ delta-based (as is any cumulative process over time): developers apply changes, and then may apply more changes at a later time - building on the state or outcome of the previous changes. They cannot, however, go back and change the actions they or others have already performed in the past. Furthermore, each action is done in terms of what already exists and what is required  (the delta).

## Specifications and releases are snapshot-based 

Specifications generally tell you _nothing_ about any previous system state, nor the changes needed to achieve the desired behaviour - only its final state _after_ the changes. This is natural for the sake of brevity, and required when there is no pre-existing functionality to reference. In this regard, they are like snapshot-based versions: self-contained descriptions without necessarily any reference to a previous state, or the number or size of changes between them.   

Similarly, releasing or deploying versions of software are also like snapshot-based version systems: they necessarily contain the information for a complete snapshot of the system. The difference is that while specifications are _prescriptive_ (how it _should_ function), releases are _descriptive_ (how the system _does_ function). 

Managers and non-technical stakeholders tend to think of software development in terms of snapshots because they input (snapshot-based) specifications, and receive (snapshot-based) releases as output. Those involved in the process in between, however, understand it is very much delta-based.

Problems arise when the software development process is managed as if it were a snapshot-based one, rather than delta-based. User stories, acceptance criteria and requirements documentation (types of specifications), are not useful for understanding the size or nature of the work to be done, who is likely to be able to perform it, or tracking its progress. This causes problems for development and management to agree on the work to be undertaken.

What is particularly at the root of many of the problems above, is the use of a _prescriptive_ snapshot-based model for measuring delta-based changes. When the system fails to completely fulfil the requirements of the prescriptive snapshot of how the system should behave, it tells us nothing about how far off it is, or the changes that need to be made to achieve it. This causes problems for quality assurance and development to agree on what state work is in, and what there is to be done about it.

## Tasks, bugs and spikes are delta-based

Refinement should be the process of taking specifications (snapshot-based), and converting it to changes to be made (delta-based). Depending on your workflow, specifications should never enter your ticketing system (but they should certainly be linked), or they enter your ticket system as a preliminary status before being refined further into actionable tickets. No developer should ever work on a ticket while it's considered still a description of a final state, rather than a description of work. 

Well written specification includes not only a final desired state, but rationale or context for why it is desirable. A really good ticket should contain information about why the _current_ system behaviour is sub-optimal, which likely requires _describing that current behaviour_ at some level, and in doing so, expressing a _difference between the two_. 

Similarly, bug tickets should begin with a description of the system's current functionality, and clearly state the desired or expected behaviour (perhaps requiring consultation with the team to clarify the intended behaviour). 

Finally, spikes should include a list of things that are currently unknown (the status quo), and that should be known by the time the spike is completed.

It is when a ticket has a description of both before and after the work is done, that the difference can be examined - along with associated context or rationale - for suitable (delta-based) changes to apply or actions to take.  Once the ticket has gone through this evolution, the steps to apply the change may be discussed. Candidate solutions can be compared, and validated for whatever criteria may be appropriate (robustness, correctness, speed, cost, etc).
            
## How using a delta-based model solves problems
                                                                                                                                                                                 
The property of delta-based systems that _changes can only be additive_ is incredibly clarifying when modelling software development. Tasks in your ticketing workflow should only move in one direction, and in doing so, reflect the underlying realities of the change control systems. Code has likely been committed or merged and now forms the new system state. 

Note that this does not mean that code cannot be reverted, or that actions or releases cannot be undone (rolled back), but the _act of reverting them_ is another extra?. action.

Tickets should only remain in a given state until the team members have done the work required to _move it out of that state_. If a ticket is in a state to apply the changes, it remains there until the changes have been made (irrespective of how completely or correctly those changes end up being). If it is in a state to be tested, it remains in that state only until it's been tested (irrespective of the outcome of that testing). In this way, testing becomes like code review: bugs or improvements are raised by examining a new system state and _additional changes_ are suggested, rather than expecting the system state that is already there to be replaced, as if it never existed, with something better. 

When a problem is encountered, what happens instead of a ticket remaining stalled or moving backwards, is a new ticket is created:
            
* When some of the work is discovered to be incomplete, split the ticket into a new one, that captures the work still outstanding.
* When it's discovered some of the work was done incorrectly, create a new ticket to fix the mistake
* When changes introduce a new bug, create a new ticket to fix it.
* When requirements end up being incomplete or vague, or changes have an unexpected affect or reveal further work is required, create a new ticket to describe it.
                                                
_And then the original ticket is moved on_ (likely closed).

This creates a very dynamic, fast-moving board or workflow, with short feedback loops and up-to-date reflections of project status. Tickets become smaller, more light-weight, and contain less in the way of comments and dense discussion (instead they are replaced with more tickets). This is analogous to the fact that in a well-used version control systems, commits are light-weight and frequent.

The democratisation of ticket generation also closely models the way successful open source communities collaborate, prioritise, allocate and action work.

## Knowing when to release

Managers in particular may be perturbed by the notion of tickets regularly splitting or yielding new tickets. How will they be able to track when work has been completed? This concern is based on a fallacy: software development is vary rarely ever completed, but merely done _well enough_ to be released. When a manager needs to know if work has been done yet, instead, a delta-based system can go one step further and answer _"What is still left to be done?"_ or _"If I release now, what problems will I have?"_. 

Ticket management should, however, be paired with two things:

* Zealous linking between tickets to maintain a relationship between work to be done, and any earlier tickets that may have caused it, or that it was split from
* Regular reviews of the new and outstanding tickets. At a certain point a value judgement must be made: is this bug important enough to fix? Is this improvement worth doing? 

## Limitations, exceptions, simplifications
                                           
Just as there are ways in version control of resetting, reverting and managing separate branches that sit outside the model of additive-only changes, there are circumstances where the above approach may not apply. Perhaps your quality assurance or UAT process works with feature servers, and issues with work done is better modelled by re-opening tickets, or resolving all outstanding issues before a branch is merged in and released. Examine the realities of your own processes and chose a way of modelling that aligns appropriately. 

Consider the overhead and features of your ticketing system and where compromises may need to be made. Sometimes a quick IM message and a few line change is all that is required to clear a ticket's status up and keep things moving, rather than going to the effort of creating a new ticket.

## Related ideas

[Getting things Done](https://gettingthingsdone.com/) emphasise the importance of breaking projects (snapshot-based) into constituent tasks (delta-based) and always determining a _next action_.

[Elephant Carpaccio](https://docs.google.com/document/d/1TCuuu-8Mm14oxsOnlk8DqfZAA1cvtYu9WGv67Yj_sSk/pub) illustrates how large user stories (snapshot-based) can actually be expressed as a series of much smaller stories that can be released faster, and eventually arrive at the same goal (delta-based).
