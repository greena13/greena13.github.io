---
layout: post
title: "Factories"
date: 2014-09-06 18:24:52 +0100
comments: true
categories:
---

The finer points of the different factory design patterns has always escaped me, so I decided to look it up recently and found [this rather helpful stackoverflow answer](http://stackoverflow.com/questions/4209791/design-patterns-abstract-factory-vs-factory-method). I thought I'd summarise what I learnt below (all diagrams were generated using [yUML](www.yuml.me)).

A factory design pattern in the most general sense is one that entrusts a class or object with the creation or instantiation of another object. Commonly, the class or object that does the creating is called the Factory object and the object being created is called the Product object. Bare this in mind when viewing the diagrams that follow.

## Static Factories

A _Static Factory_ is a class that defines a static method that generates various subtypes of a Product. It accepts an argument to determine which Product subtype to generate (shown as a string here, but it could be just about anything). The static factory does not need to be instantiated in order to generate a Product subtype. In some languages, Product's constructor is typically made private so it can only be called within the Product class and thereby prevent the factory from being bypassed.

If the system is later updated and a new Product subtypes is added, the static factory class must be updated to include the new Product subtype.

__NB:__ I've used underscore here to indicate a static method as yUML doesn't appear to be able to underline text.

![Static Factory](http://www.yuml.me/1e74d10f)

## Simple Factories

While static factories define a static method for generating Product subtypes, a _Simple Factory_ is a _separate_ class that defines an _instance_ method to generate instances of Product subtypes. Again, the method typically accepts an argument to indicate which Product subtype to generate. The Product constructor's visibility can be restricted to _package_ if the simple factory and product are packaged together.

The simple factory class must first be instantiated before it can be used to generate a Product instance. Similar to static factories, if a new Product subtype is added, the simple factory class needs to be updated to include it.

![Simple Factory](http://www.yuml.me/b1f57365)

## Factory Method

A _Factory Method_ class introduces another level of abstraction by defining a _Factory Method_ interface that includes a method to produce Product subtypes. Concrete factory method classes implement this interface and define the factory method to generate Product subtypes related to its (the concrete Factory Method class) own type. Again, you need to create an instance of the concrete Factory Method class before you can create an instance of a Product subtype.

If a new Product subtype is added, each concrete factory method class that needs to be able to generate the new Product subtype, will need to be updated.

![Factory Method](http://www.yuml.me/d7f2e8c3)

## Abstract Factories

The _Abstract Factories_ is useful when when you have several Product objects that are related conceptually, but not through inheritance. It adds a factory method to the interface for each Product object (each of which has its own set of Product subtypes). It can be thought of as overlapping two or more Factory Methods on top of each other.

![Abstract Factory Method](http://www.yuml.me/989345bf)
