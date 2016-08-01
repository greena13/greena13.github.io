---
layout: post
title: "Creating Cards for Spaced Repetition"
date: 2014-08-26 14:44:22 +0100
comments: true
categories: [Spaced Repetition, Anki, learning, memorisation]
---

*Spaced repetition* is a revision technique that minimises the number of exposures to a piece of subject matter required to commit it to longterm memory. This is achieved by altering the frequency in which items of revision material are shown to the user based on how easily it was recalled last time; the content is shown to the user right before he or she is due to forget it - and not before, avoiding wasting time on premature revision. [Anki](http://ankisrs.net/) is a free and popular tool available on Windows, Mac and mobile devices that allows you to synchronise your progress between all your devices via [Ankiweb](https://ankiweb.net).
<!--more-->
Anki works by showing a user a series of *flash cards* in a *deck* and prompting for a response for each one. When the user comes up with an answer (or gives up), they press a button to reveal the correct response. The user then selects whether they wish to be quizzed on this again soon (because they got it wrong, or were not satisfied with how quickly they came up with the answer) or some time further in the future (because it was too easy). The algorithm for managing what material is presented and when, is very effective and can be customised based on the user's preferred amount of material and time available for revision each day. Anki also includes a number of more advanced features that won't really be covered here.

I have recently been looking into spaced repetition to [improve my recall of a number of programming languages](http://www.smashingmagazine.com/2014/08/19/mastering-a-programming-language-using-spaced-repetition/). I have encountered good advice on maximising the effectiveness of spaced repetition by ensuring the content and format of flashcards is conducive to fast and meaningful memorisation. Here is a summary of the advice that stood out to me, taken primarily from Mattan Griffel's [Tips For Mastering A Programming Language Using Spaced Repetition](http://www.smashingmagazine.com/2014/08/19/mastering-a-programming-language-using-spaced-repetition/), Jack Kinsella's [Janki Method](http://www.jackkinsella.ie/2011/12/05/janki-method.html), Derek Sivers' [Memorizing a programming language using spaced repetition software](http://sivers.org/srs) and Dr Piotr Wozniak's [Twenty rules of formulating knowledge](http://www.supermemo.com/articles/20rules.htm). Each should be consulted in addition to reading this article for further examples and discussion.


## Common Memorisation Mistakes

### Avoiding Activated Ignorance

Spaced repetition and flash cards are for remembering things, *not learning them*. Subject material should only ever been encoded in cards after you have understood it. Committing anything that is poorly comprehended or misunderstood can be very damaging and will likely lead to *activated ignorance*, or information that has been memorised but is false or not completely true. This is a waste of time and will lead to the wrong information making itself available in your mind when it matters most; it also has the secondary and perhaps more damaging effect of providing you with a erroneous foundation in which to receive and process new information, creating more far-reaching errors in comprehension.

### Avoiding Inert Information

Only add cards to your deck for knowledge you understand to avoid *inert information*, or information that is although memorised, has not been correctly understood -- despite being under the impression you understand it. These facts or figures sit in your memory and dutifully present themselves when recalled, but when push comes to shove, offer no deeper understanding. An example of this would be slavishly memorising that TCP stands for *Transmission Control Protocol*, but having no knowledge of how this protocol actually functions or its place in communication.

To avoid this, you should develop a sound understanding of the full context of a subject before settling down to memorise its finer points. Particular attention and time should be spent on understanding the fundamentals to ensure a strong mental framework is in place to receive and evaluate new knowledge as it is encountered and memorised.

Spaced repetition should not be performed alone and content should not be memorised in isolation - learning without practicing the skills and information you are committing to memory will divorce the knowledge from its use and make it difficult to reconcile with how to make it work for you in your work and life. You need to use what you learn to discover important nuances or details in applying what you're memorising.


## Encoding Knowledge

### Abstraction and Chunking

Effectively encoding knowledge into Anki cards is vital to optimising how effective your revision sessions are. It is no surprise that simple material is easier to commit to memory and recall once its there, and so information should be formulated as simply and succinctly as possible. The number of words and concepts involved in each card should be kept to an absolute minimum.

It is possible to break a complicated concept up into smaller conceptual units through a process known as *chunking*. Your goal should be to create simple, unambiguous mental associations and neural pathways to follow each time you wish to recall something. Note that this is not the same as creating a single representation or association of a concept in your mind. On the contrary, it is beneficial to have as many of these simple pathways to the same concept as possible, to increase your chances and the number of contexts in which it is easy to recall the material.

Further to this end, concrete examples of concepts are better to encode on your cards than the abstract concepts themselves. Concrete examples stick in memory longer and if a series of examples are chosen wisely, it should be easier to induce the abstract concept when required rather than having to memorise it and use it to deduce applications later on. However, several similar cards should be created with slightly different answers when there is an important or subtle conceptual distinction or *gotcha* that you should know.

Not only does breaking up concepts into their smallest constituents benefit your brain's ability to recall it later, but it actually helps Anki's algorithm perform  more effectively because simple concepts are easier to schedule efficiently than larger, more abstract ones. This is because Anki is able to remind you of ony the concepts you have likely forgotten, leaving the ones you have memorised out. You are also more likely to forget all or part of a complicated idea and will need to be reminded of it more frequently, despite knowing some or most of the concept.

### Things You Should Avoid Memorising

Sets, or groupings of items without a clear or predefined order, are very difficult to remember and encode. Where possible, these should be converted to *enumerations*, or ordered sets, by linking items up in a predefined and consistent order. Enumerations are easier to recall than sets because a clear link between consecutive items exists that you brain can easily traverse to retrieve all items in the group.  Order should be chosen carefully, however, as it is more difficult to retrieve individual items in an order different to the one you have encoded. This order can be arbitrary, but should be easy to remember and preferably have meaning within the context of the material you are memorising, or match the order in which you are likely to need to retrieve them.

Enumerations are still difficult to memorise and should be avoided. Where possible, enumerations should be converted to *cloze deletions*, or sentences where some words have been omitted and you must fill in the blanks. These are typically easier to remember than sets and enumerations and are based on the concept of [*incremental reading*](http://www.supermemo.com/help/read.htm).

### Memory Conflicts

When memorising many concepts, you need to be aware of inadvertantly memorising something that may conflict with something you already know. This often happens when memorising a lot of numbers, whether dates or quantities, people often find a long-held memory difficult or impossible to retrieve after learning a new peice of information. These conflicts are difficult to predict and are better to be avoided by making concepts you memorised as unambiguous and explicit as possible to minimise the chance of inadvertently reallocating a particular neural pathway to new conflicting information.

### Strengthening Memories

A number of techniques exist for strengthening memories and increasing their intensity and duration. There is a great body of literature on mnemonics available for associating and linking items to make them easier to recall at later date. Alternatively, you may attempt to consciously integrate a new piece of information into you memory by linking it to a memory or memories you already have. Concepts are often easier to recall when they are defined relative to things we are already familiar with.

Furthermore, you can personalise information with experience or examples from your own life. The brain gives highest priority to information that has the greatest relevance to your life and relationships. By hooking a new piece of information to an existing memory with special significance or associated emotion, you are capitalising on this inherent prioritisation.

Memories can also be linked to contexts by creating something akin to different modes or domains for which your brain should draw memories from, given a certain environment or context. This often allows concepts to be expressed in fewer words and relative to other concepts already in a given context's conceptual landscape.

### Formatting

The formatting of cards should aim to convey concepts as quickly and richly as possible. Key concepts or words should be __emboldened__ to help them stand out and create a unique visual stimulus the brain can recognise from previous revision sessions and automatically retrieve the memory of what is on the card.

The usage of imagery enriches stimulus where text cannot and produces memories that are not only more evocative but more easily recalled due to the added sensory dimensions of sight embedded in the memory. Sounds and short video clips can be used to capitalise on this effect even further. Many individuals have predilections for certain sense and you may find, for example, auditory memories are stronger than visual ones.

### Prompts (Front of the Card)

The question or prompt on the front of each card should be phrased to mimic how it will likely be retrieved when needed. The solution to a problem should be encoded with a prompt that matches how you are likely to encounter that problem during your day-to-day work, rather than extracted into an abstraction that is likely to require cognitive effort to rework it into actionable knowledge. This is a corollary of the fact that memories stored using simple and strong neural pathways are easiest to retrieve.

Jack Kinsella [suggests](http://www.oxbridgenotes.co.uk/articles/janki_method_refined) that before creating a card, brainstorm possible uses for the knowledge and use this insight to cater how you record the information. This can be particularly effective when combined with cloze deletion; phrases can be created based on problems or situations you are likely face where you will need the information you are encoding. If it is proving difficult to come up with a situation where the material will be needed - it is probably the case that this information will not be worth remembering.

### Answer (Back of the Card)

The amount of cognitive effort required to decide if you got the correct answer or not on a card should be kept at a minimum. Answers that take too long to confirm as correct (usually because of ambiguity or concepts expressed in a wordy way) will eat into your cognitive reserves and draw out revision sessions to be longer than they need to. It is also an indication that the concept being encoded is too complicated and should be broken down into smaller, simpler ones.

If the material cannot be simplified and it is still taking too long to confirm as correct, try putting the important parts in **bold** or a larger font size to stand out more.

## What to Encode

### Finding Things to Remember

To get the most out of revising, you should first decide what material is most useful to have memorised. This is usually things that will save you time and resources you would otherwise spend to look it up or find out; it is a good idea to save things you would normally have to bookmark or search for on a regular basis, or insights that save you considerable time but are difficult to remember because they are rarely used.

It is good to get into the habit of creating new Anki cards for every time you learn something new or are reminded of something important. It is often worth tagging these cards to describe their content to allow for focused review when the need later arrives.

It is also a good idea to create cards with what you learn from your mistakes; performing a post-mortem after a big project to identify what went well and what did not is a good way to identify lessons to be learnt (and remembered) to put in your Anki deck. It is an even better idea to analyse progress during regular intervals throughout a project to make sure you have not lost sight of a greater picture and add these insights to your deck. A similar approach can be applied to errors encountered in every day work; solutions to common or time-consuming problems are terrifically useful things to have memorised.

Restricting what you memorise to only what you need to complete a particular project is a good way to help focus your memorisation and avoid wasting time on material that will ultimately prove of little or no use.

## Practicing

Spaced repetition software works by offering you a reminder of information just before you are most likely to forget it, both preventing you from wasting time revising the material before you need to, and avoiding you forgetting the concept completely and having to relearn it. In order for the algorithm to be the most effective, you must complete the suggested revision *every day*. This is less challenging than it sounds when you incorporate checking your deck into your daily routine. If left neglected for too long, the number of items due to be revised grows and it quickly becomes too difficult to catch up.

## Deck Maintenance

A spaced repetition deck should not be thought of as a static resource, but instead should be maintained continuously. Cards should be updated that are no longer true or, given new insight, can be improved, split or merged.

Material that is proving difficult to memorise should be rephrased, reformatted or reworked to try and make it easier to remember. Conceptual links to related material should be highlighted to strengthen associations. The content should be carefully considered, practiced or tested to help find more effective ways of encoding it in the deck and make it easier to remember.

When creating cards, it is a good idea to provide references to the sources you used and the date you last checked or review it. This is to record where to go to get more information and to clarify or expand your knowledge of a given subject  and to guage how stable or obsolete a particular piece of knowledge is.

## Prioritising What You Should Practice

Unless you want to be spending hours each day reviewing - and likely not even then - you will not have enough time to memorised every piece of knowledge you ever wanted at your fingertips. Material must be prioritised and those priorities, like the deck itself, should be regularly reviewed and adjusted. Anki's tags system can be used to filter out which cards are included or filtered out of your deck.

Material that is used regularly or is important for only a given period - perhaps while you complete a particular project - should be retired from your deck in favour of information more likely to be useful in the future. The excluded material can be re-included and rapidly re-learnt using Anki's drill feature, should there come a time when the material is required once more.

You will need to find your own balance of how long you are prepared to spend revising each day, and how useful the information you're committing to memory is likely to be.
