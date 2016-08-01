---
layout: post
title: "10 things good front-end developers do"
date: 2016-07-21 19:08:15 +0100
comments: true
categories: front-end development
---
Without getting into the details of JavaScript, HTML, CSS, or any particular framework, there are some general things that most good front-end developers do and understand.
<!--more-->
The list that follows is intended as a jumping off point. Googling each concept will help you locate more context and the original authors; most originated from well renowned books on software engineering from the last century and are not new ideas.

## Know what you are doing

A developer should be aware at all times of what type of work they are currently doing and why. This helps with focus and protects against accidental multi-tasking, which is a sure way to get lost and introduce bugs due to forgotten loose ends.

A developer is usually performing one of just a few types of work:

- **Refactoring** - improving the structure of code without changing how it functions. It is also important to know if the goal in doing so is improvements in performance, readability, structure or testability.

- **Bugfixing** - making corrective changes to the way the system functions. There is a difference between how the system currently functions and how it should and it's the developer's job to close the gap.

- **Changing functionality** - altering how the system behaves to meet new requirements. Similar to bugfixing, but the intended way the system functions has changed.

- **Adding functionality** - adding new behaviour, while leaving the existing behaviour intact.

- **Addressing non-functional concerns**: not changing the way a system behaves as it is seen by the end user, but adding or changing something like logging, analytics, monitoring or fault tolerance

The most valuable thing about a developer being mindful of what they are doing, is it helps them identify when they done. In particular, they should be able to answer the 3 important questions:

- What changes do I need to make?
- How will I know when those changes have been successfully and completely made?
- How will I know I have not caused any unintended side effects?

## YAGNI

If a feature is not currently needed, chances are You Ain’t Gonna Need It and building it now is likely to mean time wasted or mistakes made. This concept is from Extreme Programming (XP) and prescribes building a feature only when it is certainly needed and as close to when it is required as possible, to ensure the most current understanding is used.

## Single Responsibility

Code modules of all levels of abstraction should have a single responsibility so when the requirements change, only that module must too. This is difficult to evaluate because it deals with unknown changes and it is challenging to consider things you are not aware of. I try to come up with a few hypothetical changes to the requirements and mentally walk through the modifications that would be needed to ensure I have made the right code design choices. This can feel at times like a contradiction with the YAGNI principle, so care must be taken to ensure considered design choices, without over-engineering the code for an eventuality that may never happen.

## Keep things DRY

This one comes from the Ruby world and stands for Don’t Repeat Yourself. The same values, algorithms and functionality should never be repeated twice. Instead there should be an authoritative definition for each concept that is referenced whenever it is needed. Code should be refactored to abstract commonalities into reusable routines or modules and called in a way that expresses only the difference between the different use cases.

## Defensive Programming

Program for failure. Especially those that come from outside your code. What happens when that external service goes down, or someone calls your module with incorrect arguments? The boundaries between programs - whether they be structural, network or user interaction - should be carefully examined to ensure all possibilities are handled appropriately if something unintended occurs.

## Separate imperative programming from declarative programming

Imperative and declarative programming are two approaches to programming (but not the only ones). Imperative programming includes both what a program should do and how it should be done. Whereas declarative programming only describes what should be done and leaves the how to a lower level of abstraction: DSLs, SQL and regular expressions are popular examples. This allows a program to be written without concern for resource management or re-inventing common functionality as it is all handled by the lower level abstraction. It generally leeds to simpler programs and less bugs. However, if no lower level of abstraction is available, you should create your own by separating the imperative details into modules, packages, or functions that provide a nice declarative interface.

## Know the basics of Structured Programming

Structured programming is a formal system that prescribes a series of models and rules governing program structure and control flow. In particular, it is most helpful to understand how and when to use the 3 primary ways of connecting statements:

- **Sequences**: sequentially executed statements and subroutines

- **Selections**: conditionally executed statements and subroutines, depending on some system state

- **Iterations**: repeatedly executed statements and subroutines until some system state is achieved or all members of a collection are exhausted

And the corresponding 3 control structures:

- **Subroutines**: groupings of code that can be invoked using a single statement (procedures, functions, methods)

- **Block structures**: collection of statements invoked together under the same conditions

- **Loops**: collection of statements that are invoked repeatedly together until a particular state is reached (for and while loops)

Note that throwing and catching exceptions are absent from this list and were actually discouraged by Structured Programming, but have since proven that their benefits outweighs the complexity they add and are regularly used in modern languages.

## Inversion of Control

Described by Robert C. Martin & Matin Fowler, Inversion of Control underpins most development I do from day to day.

Procedural programs consist of statements and routines that are executed according to one of the 3 ways mentioned above (sequences, selections, iterations) and the control flow of the program remains with the main routine itself. In a system that employs Inversion of Control, the main routine delegates control to other modules, which return it when they are done.

This approach summarises how many of the modern web development frameworks work. They define a main structure, but allow you to write modules for rendering responses or handling events that contain their own logic for how to function. As long as they implement a certain interface, the modules are free to control the framework to meet their specific needs - whether it be to redirect, render an error message or some other task the framework provides. This makes the framework more modular and extendable and enforces a consistent architecture that allows the developer to focus on just a single module’s responsibilities.

Understanding and recognising instances of this paradigm can add clarity to design decisions.

## Keep your code simple

Code should be written for humans first and computers second. Readability has a direct and significant effect on maintenance speed and costs. The human brain tells stories and reasons in a linear fashion and can quickly make sense of code structured in the same way. It’s often possible to reduce nesting and abstract away iteration by separating nested routines and loops into descriptively named functions and methods. This has the dual purpose of making the code more sequential and abstracting away irrelevant details.

Steve McConnel’s *Code Complete* and Robert C. Martin’s *Clean Code* are two fantastic resources on this.

## Carefully order and label code

You should be aiming to facilitate quick code navigation by providing useful levels of abstraction and descriptive labels for subroutines. Ensure routines do not have side-effects that are not apparent from their name. If it is difficult to succinctly name a routine, it is a sign of unclear intentions for that routine and it is probably time to consider refactoring it.

Position related routine definitions together and reflect dependencies by placing the dependent methods just above those they make calls to. Use whitespace to group and show relationships between steps in sequences and make it easy to track dependencies through a procedure.

Keeping a clear purpose and ensuring code is readable and well structured are just some of the concerns a front-end developer faces: design and requirements engineering before starting programming and reviews and testing afterwards, come to mind. But in my experience these are the ones that yield the greatest rewards if mastered - and the most pain if neglected.
