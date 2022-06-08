---
layout: post
title: "Using CSS and Sass to encode measurement types"
date: 2022-06-04 09:52:36 +0100
comments: true
categories: 
---

CSS requires properties to be expressed in low-level units that are readily available for the machines evaluating them: integers (positive and negative) and decimals (floating point numbers). These are not, however, the optimal units to express many measurements. It's up to developers to have the awareness and discipline to select and encode them in a way that expresses the semantics of the underlying properties. 

<!--more-->
   
## Physical properties of a virtual space

An application renders in a virtual space and can be expressed in terms of its dimensions:
                                                                                                                
* **Layers** (comprised of **Planes**) cover how the different levels of a design or application should be rendered: what sits on top when two or more components occupy the same horizontal and vertical space. This is the z axis in the cartesian plane your application renders within.
* **Layout and spacing** (comprised of **Dimensions**) express the application's behaviour in the x and y axis: the sizing of elements and the spacing between them. 
* **Theme** (comprised of **Colours**) dictate what colour, texture or transparency the layers have.
* **Typography** (comprised of **Fonts**) covers what information we choose to encode on the surface of these layers.

## System properties
             
In a virtual space, you can _measure_ properties (in reality, when designing or developing an application, you are actually doing the reverse and  _defining_ them). 

### Measurement types

There are 4 types of measurements:

* **Normal** measurements have no magnitude or order (e.g. categories)
* **Ordinal** measurements _have_  a magnitude and a relative order but the interval between them are not necessarily equal (e.g.  _small_, _medium_ and _large_)
* **Interval** measurements has _both_ magnitude and equal intervals, but lacks an absolute 0: there is no notion of "none" (e.g. temperature)
* **Ratio** measurements have all 3 properties: magnitude, equal intervals, and absolute 0 (e.g. age, weight, etc)

### Choosing the right measurement type
                              
A useful way to choose which measurement type you need to express a particular design property, is to consider which operators make sense:

* **Normal** measurements don't support any comparison or arithmetic operators (only set operators, such as _inclusion_ or _exclusion_)
* **Ordinal** measurements support comparison operators (_greater than_ or _less than_)
* **Interval** data supports comparison and arithmetic operators, but has no absolute zero
* **Ratio** data supports all comparison and arithmetic operators, and has an absolute zero

### The Insertion Problem 
              
The type of measurement dictate how the list of available values may be extended:

* **Normal** measurements allow addition and subtraction of values to the possibility space, without the need to reorder (e.g. a new colour can be added to a palette with out disrupting any existing ones). 
* **Ordinal** measurements require you to reorder or redefine adjacent values if you add or subtract from the possibility space in the middle of the ranking (e.g. if you add a new font size between "medium" and "small"). We'll call this the **Insertion Problem**.
* **Interval** and **Ratio** data also have The Insertion Problem and don't allow the addition of new values (you can't add a new value in the middle of a list rankings from 1st to 3rd). You must redefine the values, changing the interval between them, or shift all measurements up or down, to make room. 
* **Ratio** data furthermore doesn't allow the expansion between existing values and absolute zero (you can't have an integer that's less than 1, but more than 0). We'll call this particular variant of the Insertion Problem the **Smallest Value Insertion Problem**
                             
## Using normal measurements

Don't name your variables to suggest ordinality for normal data:

```scss
$color-1: #0e3a5f;
$color-2: #12b8d5;
$color-4: #843534; 
```

## Using ordinal measurements

It can be common to see CSS quantities expressed as ratios when the underlying measurement is really ordinal. The `z-index` property is a good example: it supports positive and negative integers and if left unchecked quickly blooms into 10s or 100s of different values over time, as developers attempt to get exactly the right value to make it appear above one thing, but below another.

Instead, you can use Sass to turn it into ordinal data (in effect reducing the possibility space down to fewer, representative values):

```scss
$plane-1: 1;
$plane-2: 2;
$plane-3: 3;
$plane-4: 4;
$plane-5: 5;
```

### Solving the Insertion Problem
                 
The naive approach when using ordinal measurements is to do so for the values _and_ their names. This results in the ordinal **Insert Problem**: Continuing on from the example above, what if deep into development, we discovered we did need a plane between `$plane-1` and `$plane-2`? We'd have to define a new `$plane-6` and go through the codebase and find-and-replace every instance of `$plane-2` through `$plane-5`, bumping them up one each time, to vacate `$plane-2` so we could use it.

We have the same problem when using size-descriptor ordinal values:

```scss
$font-size-sm: 8px;
$font-size-md: 16px;
$font-size-lg: 24px;
```

You can insulate yourself against this possibility by using _widely dispersed_ ratio values for both the underlying values and the variable's name (i.e. don't use consecutive values):

```scss
$plane-100: 100;
$plane-200: 200;
$plane-300: 300;
$plane-400: 400;
$plane-500: 500;
```

Now if we need a plane between `$plane-100` and `$plane-200`, we can simply add a `$plane-150`:

```scss
$plane-100: 100;
$plane-150: 150;
$plane-200: 200;
```

At this point, you may question the wisdom of including the value of the variable in the name of the variable itself. It turns out this is just coincidence in the case of z-index values, and falls away when applied to other value types:

```scss
$font-size-100: 8px;
$font-size-200: 16px;
$font-size-300: 24px;
```

### Solving the Smallest Value Insertion Problem

If a font was added to the designs that was smaller than `$font-size-100` we _could_ define a `$font-size-50`, but this is a technique best reserved for insulating us against relatively ad-hoc or unpredictable deviations from standard sizing. We could have allowed ourselves some growing room by choosing a more sensible starting point: in the middle.

```scss
$font-size-400: 8px;
$font-size-500: 16px;
$font-size-600: 24px;
```

Now the ordinal data is free to grow in both directions and the addition of a smaller font is not a problem:

```scss
$font-size-300: 4px;
$font-size-400: 8px;
```

### Some other twists

It is more unorthodox, but you could also use letters instead of numbers:

```scss
$font-size-E: 4px;
$font-size-F: 8px;
$font-size-FG: 10px;
$font-size-G: 16px;
$font-size-H: 24px;
```

Or stick with integers, but reducing the dispersion (it's unlikely you're going to need so many "in between" values):

```scss
$font-size-40: 8px;
$font-size-45: 10px;
$font-size-50: 16px;
$font-size-60: 24px;
```

Or use the concatenation strategy (used with letters above) to denote in between values (arguably this is more confusing):

```scss
$font-size-4: 8px;
$font-size-4-5: 10px;
$font-size-5: 16px;
$font-size-6: 24px;
```
