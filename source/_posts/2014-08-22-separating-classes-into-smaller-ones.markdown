---
layout: post
title: "Separating Classes into Smaller Ones"
date: 2014-08-22 09:05:53 +0100
comments: true
categories: [Code Complete, classes]
---

One of the more useful things I took from Steve McConnel's [Code Complete](http://www.amazon.co.uk/Code-Complete-Practical-Handbook-Construction/dp/0735619670) is a procedure for systematically extracting smaller classes from a large one.

A common object oriented antipattern is the *God Class* or *Blob Class* which occurs when a class grows to become a large, monolithic blob with no clear single responsibility, leading to problems with maintenance and understandability. At this point, the corrective course of action is to refine the single unwieldy class into multiple smaller ones with a more manageable size and clear responsibility. The exact number and shape of these refined classes is not always immediately apparent, however it is possible to break the process up into smaller steps, allowing you to focus on one stage at a time.

1. Begin by looking at the methods themselves rather than the class and identify any that are particularly long or deal with multiple responsibilities. Separate them out into smaller self-contained chunks using vertical whitespace (empty lines); it may be necessary to shift the order of statements, particularly moving the initialisation of variables to just before they are first used -- be careful not to inadvertently change system behaviour here.

2. The whitespace-separated chunks of functionality can now be extracted into their own methods. It can be helpful to first type out the reference to this new method immediately above the statements that it will contain. This makes it easier to see what parameters the method will need by going through its statements. Once you are comfortable with the method's name and parameters, copy the reference and method statements onto the class and convert it to a valid method definitions with an appropriate access modifier (usually `private` should be your first choice).

3.  Once you have extracted as much code as possible into class or instance methods, examine each method's parameters and try to remove as many of them as possible by promoting local variables and arguments to instance or class variables so they will not need to be passed around between methods.

4. Once again, you should now attempt to separate out concerns, this time by grouping methods and instance and class variables concerned with the same functionality. Usually these can be identified simply as the methods that reference these variables.

5. Once you have identified one or more separate concerns, define a new class to encapsulate the related methods and variables. Define an instance of the new class on the blob class and instantiate it an appropriate place so you will be able to evoke the functionality of the new class from the blob class. Begin moving methods and instance variables to the new class, searching each time in the blob class for any references you may need to update (by pointing to the variable containing the instance of the new class). Continue this until you have extracted all the methods and instance variables you need to simplify the blob class.

Ideally, all refactoring should be completed with a set of comprehensive unit tests at your back to verify no functionality was changed or lost in the restructuring. This is often not the case, in no small part because God Classes are notoriously difficult to write tests for. However, the same is not true of the new, slimmer classes and it is a good idea to see the refactoring as an opportunity to outfit the new classes with the full unit test treatment; you will thank yourself when it comes time to maintain them in the future.
