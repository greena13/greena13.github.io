---
layout: post
title: "Imperative Programming vs Declarative Programming"
date: 2014-08-31 10:37:39 +0100
comments: true
categories: ["Programming Concepts", "Structured Programming", "Imperative Programming", "Declarative Programming"]
---

I was recently reading the Wikipedia articles of [imperative programming](http://en.wikipedia.org/wiki/Imperative_programming) and [declarative programming](http://en.wikipedia.org/wiki/Declarative_programming) to brush up on my understanding of the distinction.
<!--more-->
## Imperative Programming

Imperative programming is concerned with describing how a program should achieve its functionality; not only *what* should be done, but also *how* it should be done is encoded. The movement of data and execution of operations are explicitly specified, if only at an abstract level; that is to say, an imperative program is concerned with implementation as much as it is logic functionality.

Imperative programming has its roots in assembly language and machine code, however, modern high-level languages offer more human-friendly syntax and aggregate functionality. [Procedural programming](/blog/2014/08/31/procedural-programming) is a common paradigm that falls within imperative programming.

## Declarative Programming

Declarative programming focuses on expressing the logic of a computation without needing to specify it in terms of low-level operations. Declarative programming emerged out of an attempt to minimise side-effects and focus on a program's primary function, leaving the implementation details to the language itself.

Common examples of declarative programming are SQL, regular expressions and functional programming.

It seems the difference between the two programming ideologies is largely concerned with the distribution of responsibility: in imperative programming the programmer is responsible for specifying logic functionality and implementation details while in declarative programming the language takes ownership of the implementation.
