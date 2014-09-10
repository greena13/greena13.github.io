---
layout: post
title: "Getting to better code reviews"
date: 2016-01-17 12:36:29 +0000
comments: true
categories: code review 
---

I recently set out to improve the process I use for reviewing code that myself and others have written. I wanted to focus on being more systematic and getting  consistent results for the time investment.

The reviews I was doing appeared to be going well enough: they were picking up problems and I was making sensible suggestions for how to improve code quality; less bugs seemed to be finding their way into the final product when I did the reviews than when I didn't and I felt more confident about code that had been through review. But I began to suspect that the quality of these reviews were variable and subject to random and unpredictable human factors.

And although previous attempts to address this had left me with a checklist of common problems to look for, holding all thirty or so items on that checklist in my mind at once and trying to search for them all at the same time was too much complexity to effectively handle. I was applying this list in an attempt to be more consistent, but becoming overwhelmed and developing only a superficial familiarity with the code. Too often I was falling back on heuristics, like whether it looked correct, or whether I could spot anything obviously wrong with it (which are two ways of saying the same thing).

What's more, once the review was complete, there was no clear indication of the relative importance of the problems identified. Just because some problem had jumped out at me, there was no guarantee that it was the most important deficiency in the code and there wasn't a greater problem further upstream.

So I developed a system that simplified the list by grouping it into phases that made sense and allowed me to focus on looking for a single class of problem at each point in the process by running each piece of code through a series of three mental filters.

# Motivations and context
                                   
The first phase is about establishing a high-level understanding of what the code change is, and why it's being made.

There are three reasons for making a change to a software system:

- To fix a bug (when the system currently performs in a way that is considered undesirable and a change to the way the code functions is required)
- To add a new feature (when new functionality needs to be added, usually leaving the existing functionality intact)
- To make a code improvement (when the current code functions correctly, however it can be cleaned up or made to function more efficiently, but still give the same behavior)

Generally speaking each code change should attempt to do only one of the above, to avoid losing sight of why the change is being made. If you attempt two or more, it is much harder to verify the change is correct and has no unintended consequences.

I try to ask myself three questions:

- What changes need to be made to get the desired result?
- How do I know the right changes have been made and I am getting the right result?
- How do I know that nothing else has been broken as a consequence of the change?

During this phase you can be looking for opportunities to increase how easy the code is to understand. Be on the look out for: 

- Code that needs commenting
- Poor naming of variables, classes and functions 
- Missing whitespace that could help group together code concerning the same semantic operation

# Functional concerns

The second phase is where you mentally break up the code into sequences, selections and iterations to get an overview of the structure and how the program flows. It's important to get a sense of:
 
- The full domain of input values
- The number of execution paths and the conditions that must be satisfied for each
- The stopping conditions for loops 
- All of the possible exit points, including those from exceptions that may be thrown

Once you have this understanding, it is much easier to identify:

- Logic errors
- Boundary errors
- Failures to handle exceptions and error conditions
- Implicit assumptions that may not always be true

# Non-functional concerns

Once you have convinced yourself that the code does what it is intended to do and that it is as readable and understandable as possible, then you can concern yourself with whether it is optimal from a performance, security and reliability perspective. 

- Describe routines in terms of Big O notation
- Consider which parameters are user input or from other sources that should be considered unsafe
- Identify the scope of each variable and piece of data
- External dependencies that may fail and how they are handled

This makes it easier to locate:

- Any gross inefficiencies (this is not the time for micro-optimisations)
- Poor choices of data structures and algorithms
- Scope leakes
- Security vulnerabilities

And that's it. At first this list took a long time to run through, but it quickly became much faster to execute while still detecting a lot more problems.
