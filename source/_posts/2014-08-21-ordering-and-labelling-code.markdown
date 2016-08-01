---
layout: post
title: "Ordering and Labelling Code"
date: 2014-08-21 14:53:17 +0100
comments: true
categories: [Code Complete, Clean Code, naming, ordering]
---

Reading Steve McConnel's [Code Complete](http://www.amazon.co.uk/Code-Complete-Practical-Handbook-Construction/dp/0735619670) and Robert C. Martin's [Clean Code](http://www.amazon.co.uk/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), I encountered a subject that I previously hadn't given much thought to but is of great importance. McConnel talks of reducing the cognitive load of reading and understanding code by writing for humans first, computers second; where the reader of a piece of code should be the author's highest priority - if not only because these often end up being one and the same. An important part of this is facilitating quick code navigation to allow the reader to find what they need, and providing useful levels of abstraction so the reader need not concern themselves with irrelevant details.
<!--more-->
This is achieved by making considered choices with code order and placement. Two units of code that are related in some way - the most important and obvious being references between them or shared execution paths - should be placed nearby to each other. This is to allow a reader to easily refer between the two without having to jump between separate locations in a file. McConnel offers the analogy of a newspaper for guidance on how code should be ordered: the important concepts are featured at the top while the refinements or details can be found towards the bottom.

Vertical whitespace should be used wisely to separate different ideas or functions in the code. Statements that perform actions that are part of the same larger action should be grouped together, while those that are not should be separated visually by one or more lines of whitespace. If a clear enough conceptual gap exists between a group of statements and the rest of the code, it can and often should be extracted out into a well-named method or function (and given the appropriate access modifier to correctly observe *Encapsulation*).

Care should be taken when naming methods and functions to the make the reader's task easier. A well-named block of code should make clear what it is the code does, what input it expects or requires and in the case of [multiple parameters](blog/2014/08/20/reducing-a-routines-parameters/), what order the arguments should be given in. If named wisely, a function or method should require little or no further documentation.

It is paramount that a piece of code only perform the functionality suggested by its name, free of hidden side effects or behaviour. To ignore this is to surely guarantee bugs that are difficult to diagnose and fix. However, something greater is at stake: if violated, the trust between the code's author and reader is broken and the reader is forced to then check every method or function for similarly concealed behaviour.

If it is proving difficult to accurately name a method or function succinctly, often it is an indication of an inappropriate level of abstraction or confused intentions behind the code. For example, functions that perform queries or return a system status should be separated from those that execute and action, or alter the system state.

Naming conventions can be used to imbue code with consistency and reduce the number of surprises the reader must negotiate in parsing the code. The merits of specific conventions are secondary to the benefits of consistency itself.
