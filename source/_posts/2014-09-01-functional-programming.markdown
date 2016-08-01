---
layout: post
title: "Functional Programming"
date: 2014-09-01 09:52:42 +0100
comments: true
categories: ["Programming Concepts", "Structured Programming", "Functional Programming"]
---

As part of my continuing efforts to clarify fundamental software engineering concepts, I recently read and summarised the most interesting points of the [functional programming](http://en.wikipedia.org/wiki/Functional_programming) Wikipedia page.
<!--more-->
Functional programming is a [declarative programming](/blog/2014/08/31/imperative-programming-vs-declarative-programming) paradigm that treats computations as evaluations of mathematical functions and avoids referencing a system state and mutable data. It was developed, primarily, as an attempt to avoid side effects and make subroutines much easier to reason about. It introduces a number of interesting and useful concepts that are not often encountered or emphasised in object-orient programming.

## Pure Functions

Pure functions are those that have no side effects and depend only on their argument values, not on any system information; consequently, they return the same value for a given set of parameter values every time. They have some unique properties useful for code optimisation and reasoning:

- If the result of a pure expression is not used, it can be removed without altering the code's behaviour.

- If a pure function is called with arguments that are themselves pure functions, or don't have side effects, then the result can be cached and returned if the function is called with the same parameter values again.

- If there is no dependency amongst a collection of functions, they can be run in any order with the same result.

## Referential Transparency

Referential transparency is a property of code that refers to whether or not it can be replaced with its calculated value without changing its behaviour. If this is indeed the case, the code is said to be *referentially transparent*; if the code cannot be replaced with its value without altering system behaviour then it is *referentially opaque*.

All pure function calls are referentially transparent as they do not depend on or change system behaviour and should always return the same result when given the same arguments.

## First-class Functions

First-class functions are those which can appear anywhere in a program that other first-class entities (such as numbers) can. This notably includes as arguments and return values for other functions. They can also accept and return other functions.
