---
layout: post
title: "First Five Principles of Object-Oriented Programming"
date: 2014-09-01 20:18:37 +0100
comments: true
categories:
---

**SOLID** is an acronym mnemonic coined by Michael Feathers for Robert C. Martin's "first five principles" for object-oriented programming. Each concept is worth looking at briefly so I have tried to summarise the important points of each below.
<!--more-->
## Single Responsibility Principle

Robert C. Martin introduced in his book [Agile Software Development, Principles, Patterns and Practices](http://www.amazon.co.uk/Software-Development-Principles-Patterns-Practices/dp/0132760584) the *Single Responsibility Principe*, which states code modules (whether they be classes, methods, variables, etc.) should have a single responsibility, or rather a single reason for the module to need updating if a program's specifications were ever to change. Examples of the application of this principle can be seen in the separation of *presentation* and *content* commonly performed in object-oriented systems.

## Open/Closed Principle

The *Open/Closed Principle* has always referred to the fact that modules should "be open for extension but closed for modification", but exactly what that meant has had two different interpretations.

### Meyer's Open/Closed Principle
Bertrand Meyer is attributed with creating the term *open/closed principle* in the 1988 book, [Object Oriented Software Construction](http://www.amazon.co.uk/Object-Oriented-Software-Construction-Prentice-Hall-Resource/dp/0136291554). Meyer's definition of the rule prescribed that once a module had been coded it should only be altered directly to fix errors - all other modifications should be completed by creating a new class - usually via inheritance to reuse code from the original class. In this form of the open/closed principle, the new class need *not* reuse the interface of the old class.

### Polymorphic Open/Closed Principle

In the 1990s the term was largely redefined to refer to the use of abstracted interfaces instead of concrete ones. In contrast to Meyer's version of the principle, concrete classes could be modified and swapped out, so long as the module's *interface* was closed to modifications. According to the rule, changes or extensions could be performed by altering the concrete implementations freely, so long as the original interface was persisted and only added to.

Where Meyer's principle preserved *implementation* and allowed for the modification of interfaces, the polymorphic open/closed principle preserves *interfaces* and allows for the modification of implementations.

Robert C. Martin's article in 1996, *The Open-Closed Principle*, was instrumental in forwarding this approach and it is the latter definition that is in common use today.

## Liskov Substitution Principle

The *Liskov Substitution Principle* put forth by Barbara Liskov's 1987 '*Data abstraction and hierarchy*' states that a programmer should be able to substitute an object with an instance of one of its subtypes without altering the surrounding code's behaviour. This is another way of saying that any subtype of an object must completely honour the interface and observable behaviour of its supertype.

To achieve this, a number of requirements must be met that govern the subtype's signature:

- It must have [contravariant method arguments](/blog/2014/09/01/covariance-and-contravariance)
- It must have [covariant method return types](/blog/2014/09/01/covariance-and-contravariance)
- No new exceptions should be thrown from subtype class's methods other than when they are themselves subtypes of exceptions thrown by the supertype

Beyond maintaining the same interface, a number of behavioural conditions for the subtype must also be satisfied:

- Preconditions are not to be strengthened in the subtype, i.e. the subtype must not depend on any more prerequisites than its supertype
- Postconditions cannot be weakened in the subtype, i.e. the subtype must perform the same behaviour as the supertype
- [Method arguments invariants](/blog/2014/09/01/covariance-and-contravariance) and [method return type invariants](/blog/2014/09/01/covariance-and-contravariance) of the supertype must be preserved in a subtype
- The introduction of new methods cannot allow the subtype to enter a state that would not have been possible in the supertype and thereby change its behaviour; This is often referred to as the *History Constraint*.

## Interface Segregation Principle

The *Interface-Segregation Principle* states client code should not be able to see any interface points it does not need access to in order to function. Access should be done instead via a *role interface*, which is tailored to the particular needs of the client code. In short, many specific interfaces are better than a single general purpose one.

## Dependency Inversion Principle

*Dependency Inversion* is a design pattern that focuses on decoupling software components of different abstractions. Traditionally, high-level components defined functionality in broad strokes and low-level components grew to support the high level code.

Dependency Inversion is based on the notion that rather than having code components with a high level of abstraction depending directly on code components with a low level of abstraction, both components should instead depend on an abstraction. The details of these components should be coded to the abstractions, and not the abstractions to the details.

This design pattern separates the code responsible for connecting the high-level components to the low-level components into an abstract interface, simplifying each component and allowing greater reusability. Often the components are divided into different libraries where the high-level components and interfaces for the functionality needed of the low-level code are bundled together; the low level components are given their own package. Because the interfaces the low-level components implement (and thus depend on) are bundled with the high-level code, the dependency flows up and it is this inversion of dependency direction for which the design pattern gets its name.
