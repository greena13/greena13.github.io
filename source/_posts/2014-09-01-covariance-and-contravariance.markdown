---
layout: post
title: "Covariance and Contravariance"
date: 2014-09-01 16:37:28 +0100
comments: true
categories: ["Programming Concepts", "Covariance", "Contravariance"]
---

I have found variance one of the more difficult things to get my head around in computer science, so I have tried to document the basics as a way of teaching myself.
<!--more-->
*Variance* in object-oriented programming refers to how subtyping works between complex types. For example, if *B* is a subtype of *A* and *CA* and *CB* are enumerable collections (our complex types) that contain items of type *A* and *B*, respectively, is the collection *CB* a subtype of the collection *CA*?

- If the enumerable collection is **covariant** then *CB* is a subtype of *CA* and the subtyping relation is said to be **preserved**.
- If the enumerable collection is **contravariant** then *CB* is a subtype of *CA* and the subtyping relation is said to be **reversed**.
- If the enumerable collection is **invariant** then *CA* and *CB* are not related by type at all and the subtyping relation is said to be **ignored**.

## Argument And Return Type Variance

When inheriting from a parent class and overwriting a method, a programmer is faced with a number of options for the argument and return types of that method, depending on the programming language being used.

Considering the argument type first, programming languages will either offer invariant, contravariant or covariant argument types. A language that offers *invariant argument types* (C++, Java, C#) requires the arguments of the overriding method to be of the same type as the overridden method. Languages that offer *contravariant argument types* allow arguments to be supertypes of the overridden method's arguments. Similarly, languages offering *covariant argument types* permit arguments of subtypes of those for the overridden method.

Return types are much the same; languages offer either covariant (C++, Java) or invariant (C#) return types.
