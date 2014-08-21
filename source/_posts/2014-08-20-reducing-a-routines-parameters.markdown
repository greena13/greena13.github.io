---
layout: post
title: "Reducing a Routine's Parameters"
date: 2014-08-20 21:04:08 +0100
comments: true
categories: [complexity, simplicity, function, method, parameter]
---

Steve McConnel's [Code Complete](http://www.amazon.co.uk/Code-Complete-Practical-Handbook-Construction/dp/0735619670) provides a great insight into understanding some of the common function forms and how to go about simplifying wayward parameter lists into something that clearly conveys intention and is easier to remember and use. McConnel writes, software should be written for people first and foremost and focus should be given to improving readability and maintainability, rather than performance.

First, a brief note on terminology: I will be using *routine*, *method* and *function* interchangeably to refer to a *piece of code that accepts or requires a given number of inputs, or parameters*.

McConnel points out that code with a long list of parameters present problems to those who need to understand, maintain and use it. Multiple parameters are difficult to remember when the function needs to be called - as is the correct order to use them in. Code compliant with the mandates of *Defensive Programming* will also need to check the arguments have been provided in the correct order and have sensible values. While many IDEs and languages will do the recording of correct parameter order and type checking, this should not been seen as excuse for bad practices. Long parameter lists provide further headaches for maintenance if the method's signature needs to be later modified.

A function can usually be improved by reducing the number of parameters it requires. *Polyadic functions* (with 3 or more parameters) should almost always be refactored to require fewer arguments. One way of achieving this is by breaking the function up into smaller ones that perform simpler chunks of functionality and need less information to execute; this change in abstraction can often add clarity to the intention behind the code. Another strategy for reducing a function's parameters is to wrap them up in a single data structure, removing the need to remember the parameter order completely and freeing up the reader to focus on what's pertinent to the function of the code.

Good code should consist primarily of *monadic* and *dyadic* functions, or those with 1 and 2 parameters, respectively. Monadic functions typically take one of three forms:

- Asking a question or testing a particular condition. eg. `isRed(ball)`
- Performing an action or transformation on a particular parameter. eg. `fileOpen(myFile)`
- Handling an event where the function uses the argument to update the system's state. eg. `registerMouseEvent(mouseEvent)`

It should be noted that the first form need not return a boolean value. A function that returns a particular characteristic is also of this form. eg. `averageGrade(reportCard)`. Functions of the second form should also return the result of the action or transform wherever possible. _Output arguments_, or where functions perform an action on the argument instance itself and return either `null`/`void` or another value, are more difficult to follow. In the case of an action being performed on an instance, an object-oriented paradigm is better suited eg. `ball.bounce()` is clearer than a function `bounce(ball)` that returns the instance of `ball`.

If a function or method meets one of the above descriptions and has more than a single parameter, it is a good sign that it is a candidate for refactoring.

Dyadic functions are not as easily classified into categories and can perform many actions. Care should be taken with the ordering of the parameters when defining a function or method's signature so that is natural and easy to remember and should be evident from the name of the routine. If at all possible, the pairing of the two parameters should be wrapped in single argument to convert to a monadic function.

The ideal function is a *niliadic function*, or a function without any parameters.
