---
layout: post
title: "Dependency Injection"
date: 2014-09-02 10:56:14 +0100
comments: true
categories: ["Programming Concepts", "Dependency Injection", "Design Patterns"]
---

*Dependency Injection* is a software design pattern that works by *injecting* a reference to a dependency object into a dependent one, where it is stored and made part of the dependent object's state and behaviour. This separates the creation of the dependency object from the dependent object's code. In doing so, it more loosely couples the two objects and abides by the principles of [Dependency Inversion](/blog/2014/09/01/first-five-principles-of-object-oriented-programming) (because the dependent object is coupled with the dependency object's interface, rather than directly) and [Single Responsibility](/blog/2014/09/01/first-five-principles-of-object-oriented-programming) (because the dependent object is only responsible for its own behaviour, not the creation of the dependency object).

## Design Pattern Elements

The pattern involves 4 elements:

- **Dependency Object**: A concrete dependency object
- **Dependent Object**: A concrete dependent object that relies on the dependency one
- **Dependency Interface**: An interface used by the dependent object to communicate with the dependency object
- **Injector Object**: A concrete injector object responsible for injecting the dependency object into the dependent one

The implementation of the Dependency Injection pattern is similar to that of the Strategy pattern, but the semantics differ. The same dependency object is intended to be used for the duration of the dependent object's lifetime, while a strategy often needs to be interchanged throughout an object's lifetime.

## Depdency Injection Techniques

Martin Fowler defined 3 ways of injecting a dependency:

- **Constructor Injection**: Dependencies are provided to the dependent object's constructor at the time of creation. This requires the dependency object to be instantiated before the dependent instance and is the preferred method because it avoids the dependent instance ever being in an invalid state. Dependencies injected in this way, however, don't permit the interchanging of the dependency instance.
- **Setter Injection**: The dependent object provides a setter method that accepts the dependency. This can lead to the problem of needing to make sure the dependency has been set before the dependent object is used; furthermore, it does not explicitly state the relationship between the dependent and dependency object, which is [considered bad practice](/blog/2014/08/21/reducing-code-complexity). It does, however, allow changing the dependency object later in the dependent object's lifetime.
- **Interface Injection**: The dependency interface provides a method that accepts the dependent object, for the dependency object to inject itself into the dependent object. For this to work, the dependent object must also have a setter method for the dependency object to use to inject itself. This differs from the *Setter Injection* technique because it goes one step further and makes the dependency object itself a *Dependency Injector*.

