---
layout: post
title: "Reducing Code Complexity"
date: 2014-08-21 16:00:53 +0100
comments: true
categories: [Code Complete, Clean Code, complexity, simplification]
---

I have already covered what I picked up from Steve McConnel's [Code Complete](http://www.amazon.co.uk/Code-Complete-Practical-Handbook-Construction/dp/0735619670) and Robert C. Martin's [Clean Code](http://www.amazon.co.uk/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) on [packaging and labeling blocks of code](blog/2014/08/21/ordering-and-labelling-code) and [simplifying their inputs](blog/2014/08/20/reducing-a-routines-parameters/). Now I intend to cover what the two volumes offer in regards to reducing the complexity of the shape of, and relationships between, code segments. As is consistently emphasised through McConnel's [Code Complete](http://www.amazon.co.uk/Code-Complete-Practical-Handbook-Construction/dp/0735619670), the author of a piece of code is beholden to its reader and should write for readability and maintainability, as if often ends up being the same person or a colleague.
<!--more-->
To understand how a piece of code works, the reader must often hold in their mind the several steps they traversed along a particular execution path to reach a given point in the code. It is difficult to mentally retain these preconditions while juggling the cognitive task of interpreting the commands at the point the reader is currently at in parsing the code. A way of making this easier is to reduce the levels of nesting of conditional statements, or keeping the code as *flat* and as close to linear as possible; this better matches how the human brain works and plays to its strengths and aptitude for linear storytelling and reasoning patterns.

One way to simplify a series of deeply nested conditions with many variables is to introduce a helpful level of abstraction by bundling up as many of those conditions as possible in descriptively named query methods or functions; `if isWeekDay(day)` is preferrable to `if(day != 'Saturday' && day != 'Sunday')`.

Another method is to attempt to simplify nested conditions using logic axioms such as *Demorgan's Law*. A series of if statements is often easier to navigate than a single chunk of nested logic. It also reduces the number of inputs to the block, providing greater opportunity to extract it out into separate methods or functions with clearer indications of what data influences its behaviour.

When unwrapping nested conditional statements, the preconditions for an extracted piece of code may become less clear. This should be address by making any dependencies explicit. To illustrate: if one function must be called before another, have the first function return a data type that the second one requires as a parameter. For example, if a function `throwRock()` must be called before another `breakGlass()`, make `throwRock()` return an instance of `WallPosition` and have `breakGlass()` require an argument of type `WallPosition`.
