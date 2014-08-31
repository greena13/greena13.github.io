---
layout: post
title: "Structured Programming"
date: 2014-08-30 22:11:41 +0100
comments: true
categories: ["Programming Concepts", "Structured Programming"]
---

I have been recently trying to get a better handle on some terms I possess a casual understanding of and use regularly in conversation, but suspect I don't really have a full appreciation for. *Structured programming* was the first of these terms. What follows is a summary of the more interesting bits of the [Wikipedia article](http://en.wikipedia.org/wiki/Structured_programming).

## Structured Programming

Structured programming emerged, at least in part, from the Edsger Dijkstra's paper '*GOTO Considered Harmful*', warning of the propensity for the `goto` control statement to lead to *Spaghetti Code*.

### Structured Programming Theory

Structured program theory offers 3 ways of connecting statements, which are sufficient to express any computable function, or all of the operations of a *Turing Machine*:

- As **Sequences**: sequentially executed statements and subroutines
- As **Selections**: conditionally executed statements and subroutines, depending on system state
- As **Iterations**: repeatedly executed statements and subroutines until some system state is achieved or all members of a collection are exhausted

### Structured Programming

Structured programming implements the 3 ways of connecting statements using 3 control structures:

- **Subroutines**: groupings of code that can be evoked using a single statement, eg. procedures, functions or methods
- **Block Structures**: collection of statements evoked together under the same conditions, eg. blocks designated with `if` and `end`.
- **Loops**: collection of statements that are evoked repeatedly together until a particular state is reached, eg `for` and `while` loops.

### Single Exit Point Rule

Structured programming advocates a single entry and exit point for each subroutine to make reasoning about them easier and reduce the potential for particular types of bugs to occur. Many have argued trying to abide by this principle can add unnecessary complexity and many programming languages facilitate subroutines with multiple exit points, eg. `return`, `break` and `continue`.

When coding subroutines with multiple exits, care must be taken to ensure that cleanup code is run at the end of all execution paths (think of the `finally` block provided by some programming languages for exception handling).

The computer scientist David Watt considered the justification for avoiding `goto` statements (Watts termed them *jump sequencers*) in [his textbook](http://www.amazon.co.uk/Programming-Language-Concepts-Computer-Science/dp/0470853204). He suggests `goto` are harmful to the understandability of code because the reader must first go to the memory address or subroutine definition to find out the intent behind the jump; however, Watt argues the intent behind what he terms _escape sequencers_ (`return`, `continue` and `break`) is much clearer and therefore do not create the same problem.

### Exception Handling

Exceptions pose a particular challenge for trying to abide by the single exit point rule. Watts notes that *exception sequencers* usually need the exception handler to reside at a more contextually aware area of the code, away from the low-level code where it occurs (the exception is *thrown* or *raised*). Kenneth Louden and Lambert add in [their textbook](http://www.amazon.com/Programming-Languages-Principles-Kenneth-Louden/dp/1111529418) that exception handling is not structured programming because the setup of control and the actual transfer of control happen in different places in the code. Furthermore, [it has been noted by some](http://en.wikipedia.org/wiki/Structured_programming#cite_note-Bansal2013-13) that exceptions can even cause control structures that would normally conform to the single exit rule to violate it: for example, a `for` loop where an exception is thrown in the instantiation of the first iteration - the loop would not get to run and exit as intended.

### Single Entry Point Rule

Routines can also have multiple entry points and violate the single entry rule, most notably as re-entry into a *coroutine*; this happens when a subroutine yields control to another but maintains its own state and can be resumed where it was left off. This has applications in input/output and network streams as well as concurrency and state machines in general.
