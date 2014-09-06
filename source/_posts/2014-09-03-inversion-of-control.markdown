---
layout: post
title: "Inversion of Control"
date: 2014-09-03 08:52:19 +0100
comments: true
categories:
---

Popularised by Robert C. Martin and Martin Fowler, *Inversion of Control* is a design principle where *custom code components* receive the control flow from general *library components*, similar to custom code modules being written for a larger general-purpose framework. This is an inversion of the traditional control flow found in [procedural programming](/blog/2014/08/31/procedural-programming) where the custom components encode the program's functionality in terms of general purpose libraries. The pattern offers the advantage of making the program more modular and extendable.

Inversion of control is related to Martin's [dependency inversion principle](/blog/2014/09/01/first-five-principles-of-object-oriented-programming), which is concerned with having high and low level code depend an abstraction, rather than directly on each other, thereby inverting the direction of dependency. This is analogous to the way high-level, general components yield control to the low-level specific code in Inversion of Control, thereby inverting the traditional flow of control.

Although the term is most often used in the context of object-oriented programming, there are design patterns with wider applications that follow the Inversion of Control principle, such as software frameworks, callbacks and [dependency injection](/blog/2014/09/02/dependency-injection).
