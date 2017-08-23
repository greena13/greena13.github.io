---
layout: post
title: "Emojis are the solution to useless commit messages"
date: 2016-08-19 18:32:38 +0100
comments: true
categories: git commit message emoji emoticon
---

In most git clients, commit messages are truncated after a single line. This is not enough space to fully describe the changes a commit contains - and to attempt do so would be misguided. A description that imparts as much detail as reviewing the changes themselves offers little in the way of useful abstraction. But these limitations may be overcome.
<!--more-->

The need for brevity can be partially addressed by ensuring descriptions are written so they read like steps in a set of instructions for how to modify the code: e.g. *Remove file X* is preferable to *Removed file X* or *Removing file X*. This leaves out distracting words and tense.

The problem remains, however, of deciding what information is useful and at what level of abstraction it should be expressed. This leads to inconsistencies in specificity depending on the nature and size of the changes, making it difficult to form any high level judgements about the code short of reading through the changes of each commit. A reader cannot ascertain the *type* of changes made, nor the reasons for making them, just from the commit's message. 

Separate systems are maintained for tracking features and fixes (and the rationale behind them) but they deal in much higher levels of abstraction; they don't explain what a developer was hoping to achieve when they moved a method or renamed a constant. This information can be recorded in comments, but often only really makes sense within the context and scope of a change. Such comments only highlight what the code used to be - not what it is - and are either irrelevant or obsolete.

These problems can be addressed by labelling commit messages in a way that can help establish the nature of the changes and guide developers as they work. The commit author is then free to describe its details in the remaining space available, as normal.

This could be achieved by prefixing each commit to signify useful information about the changes it makes. Mnemonics, or abbreviations, have the benefit of consistency and accessibility, but are difficult for developers to memorise and use. They also lack the visual distinction to quickly tell the difference between similar strings of characters.

Emojis are perfect for capitalising on the brain’s ability to easily parse and distinguish between visual symbols. They also take up little space - a single character position each - and are designed to quickly impart concepts and ideas, yet be versatile enough to have different meaning depending on context. They are therefore more succinct and expressive than short mnemonics created from a series of letters.

## Related work 

Emojis have been used in commit messages for years now, helped along by [Github offerring support for them using printable characters](http://www.webpagefx.com/tools/emoji-cheat-sheet/). However, I have not encountered them being used consistently or to convey as extensive a range of data as what is described below. It is worth noting that the emojis offered by Github are not as extensive as I require and although they are guaranteed to appear consistently on Github, they will not display at all in many git clients, instead rendering the longer sequence of characters that denote them - which cuts into the space afforded to the commit message itself. Git client support for them is likely to improve, however, so you may decide that staying within those offered by Github is right for you and your team.

The system that follows is an amalgamation of an earlier version of itself and [Mohammad Shokri’s commit emojis](https://github.com/slashsBin/styleguide-git-commit-message) - the only other resource I was able to find online along similar lines. There are more than a few changes, exclusions and additions to fill some perceived gaps.  I believe Shokri remains within the Github emojis. 

## Commit Emojis

Each emoji is assigned a specific meaning and is a member of a larger group, intended to allow the reader to perform certain reasoning about the commit without actually reading its details.

## Change Types

The best commits are atomic, have a clear focus and allow the option to revert without breaking or depending on adjacent commits. A developer should aim to have commits that are wholely described by a single *Change Type* emoji - combining them with one or more *Concern* emojis discussed below. Two *Change Type* emojis are often symptomatic of attempting to make too many changes at once - increasing the likelihood of mistakes or going too long before testing.

### Refactoring

Refactoring is improving the structure of code without changing how it functions. Code that is successfully refactored has the same logical functionality as it did before and can be considered to have no behavioural changes. It does however, improve one of a few dimensions:

- 🚀 **Performance** the code uses less of a given resource, whether it be time, memory, iterations or some other quantity. The system works more efficiently at performing the same function it did before.
- 👓 **Readability** the code is made easier to understand or read. This can range from re-ordering method definitions, adding or removing whitespace, introducing descriptively named routines or any other means of conveying meaning or clarity that does not change the way the system behaves.
- 👷 **Structure** the components of the system are restructured so that responsibilities are reassigned or to build a better basis for further expansion. The system as a whole behaves the same, but the parts that it comprises of and how they work together may change.
- 🔬 **Testability** improves the ease in which components can be tested together or in isolation. This usually involves altering how dependencies are resolved and passed around, allowing the substitution of system components for test mocks or spies.
- ☂️ **DRYing up code** finding instances of duplication of data or behaviour and consolidating them into a single source of truth that is referenced each time. The system should perform the same behaviour but with less code to maintain.
- 🌱 **Extracting code** relocating code that may be re-used or has its own concerns to a separate module. The external behaviour is preserved, it is just achieved using a new dependency.
- ✂️ **Removing dead code** deleting code that is never executed or has no effect on the system’s behaviour.

By attaching any of these labels to a commit, it tells the reader that - according to the developer's understanding at the time - the code should have the same behaviour after the commit as it did before. It clearly indicates both the developer’s intentions and the nature of the changes.

It now becomes possible to quickly examine the commit history - whether manually or by some automated means - to see how much work has been going into improving the design and performance of the codebase. This can be useful for keeping tabs on the growth of technical debt and draw attention to problematic areas of a system.

### Functional Changes

Functional changes are those made to how a system behaves: it changes what output will result from one or more input values. There are two main reasons for such changes:

- 🐛 **Bugfixing** making corrective changes to the way the system functions. There is a difference between how the system currently functions and how it should and it’s the developer’s job to close the gap.
- ⚙ **Changing functionality** altering how the system behaves to meet new requirements. Similar to bugfixing, but the way the system is intended to function has changed rather than just the system failing to meet that intention.

These emojis clearly label the system as changing and give an indication of the resources being spent correcting bugs and changing existing functionality. Functional change emojis should **not** be used in the same commit messages as those in the Refactoring group. Perform refactoring and functional changes separately.

### Adding Functionality

Separate from maintaining the existing behaviour of a system is adding new behaviour - usually while leaving the existing behaviour intact. It falls into one of a few categories:

- 🌟 **Adding new functionality** new behaviour is being added that is separate from the concerns of the existing system. It does not break or compromise any dependencies, nor does it change how any facet behaves.
- ⚡️ **Adding non-backwards compatible functionality** this is new behaviour but it is intended to change or modify the existing behaviour of the system.
- 🕹 **Prototyping** an idea is being explored that may grow into a feature in the future. It is likely this code may be abandoned, or if it is built upon, it will likely need heavy refactoring and improvements.

These emojis indicate effort spent on expanding the system as opposed to maintaining and modifying it. In the case of Prototyping, it also indicates exploratory efforts that will likely become sources of technical debt if adopted without proper re-writes.

### Infrastructure changes

Often your software system is linked with dependencies and compiled according so some environment-specific settings. These changes do not modify the system directly, but can indirectly affect how it behaves through compiler arguments and the bundled libraries and assets to include.  

- 🔧**Changing build settings** changes to the way the project is built or developed. This includes establishing and maintaining different build environments and developments tools. 
- **Dependencies**  ➡️ adding, ⬅️ removing, ⬆️ upgrading and ⬇️ downgrading dependencies often have a big impact on the behaviour of a system and should be marked clearly to make it easy to track when the libraries that your system depends on are changed. This makes it easier to identify the cause of newfound issues and to keep track of the addition of more prerequisites.

### Non-functional Changes

There are many changes to the code that do not effect the way a system functions, but instead are concerned with the supporting systems that help with development, maintenance and monitoring:

- 📚 **Documentation** information included with or amongst the code intended for either maintainers or users of the system. This can include comments or Readmes or some other markup of the system and its behaviour.
- ✅  **Increasing test coverage** Adding more tests or improving the existing ones. These usually follow the addition of new functionality to the system.
- 💚 **Fixing & updating tests** changes to the test suite are often required after changing existing behaviour.
- 👕 **Linting** changes to the rules used to perform static analysis during development.
-  **Logging** 🔔 adding and 🔕 removing logging, or changing the log level.
- 📈 **Analytics** adjusting what event tracking is included in the system to better understand how it is being used by users.
- 🔭 **Monitoring** adjusting integrations with monitoring services.
- 🎥 **Devops and Production environment configuration** often the production build for a system involves additional optimisations, removal of development tools and other specialised configuration which must be added and maintained from time to time.

### Milestones & Stability

In an earlier version of this system there were many more states, including a WIP state. I found this encouraged committing partially complete and broken code and ultimately was an exercise in documenting developer ignorance. There was little value in recording a developer’s understanding of the code’s stability at the time of completion  - because that is precisely when a developer knows the least about it. Code reviews, belated realisations and changing requirements often caused the commit to be mislabelled in retrospect.

Yet, two important states remain that should be quite clear at the time of committing the changes:

- 💀 **Unstable** the code is known to be unstable or seriously incomplete, yet must be committed anyway because of some extenuating circumstances. This serves as a warning to all to not check out this commit and resume work.
- 📦 **New version** indicates the closing off and releasing of a version. It is in this commit that the new version string is saved and it this commit that is tagged.

## Concerns

### Reasons for Changes

If you are changing or adding functionality, there are some common motivations for doing so that are worth denoting. This is not an exhaustive list but includes the cases that are most often encountered:

- 💬 **Code review** suggested changes that have come out of code review. They provide a useful indicator of how successfully and regularly reviews are being executed and acted upon.
- 👾 **Supporting another platform** this can be a device, operating system or software client. It is useful to label changes made to accommodate different environments to see how much work is being spent ensuring existing behaviour is accessible across all platforms.
- ⚔ **Defensive programming** covers anything that involves catering for the unexpected or miss-use of a system. This could be adding additional exception handling, catering for when an external service is down, or parameter checking to ensure helpful errors are raised when appropriate.
- 🔒 **Security** whenever changes are made to make the system more secure. This can range from adding additional sanitisation to upgrading dependencies with known security holes. These should be labelled clearly and obviously so it is apparent what concerns or problems have been addressed.

### Common System Areas

It can be useful to label what general areas of a system a commit makes changes to so that you may gauge where to focus testing or to expect new functionality.

- 💄 **Styling** these are superficial changes that represent modifications to how something appears to an end user. These should not include changes to the system’s core behaviour, just how that behaviour is represented.
- 🎩 **Admin features** changes that are not part of the system as the end user experiences them, but are instead part of the administrative features of the system.
- 📊 **Database changes** represent modifications to the data or the database that the system may maintain locally or remotely.

## Using the emojis

Select as many of the *Concerns* emojis (*Reason for Changes* and *Common System Areas* emojis) as are applicable and then select one *Change Type* emoji. If two or more *Change Type* emojis are applicable, attempt to separate out the commit into several smaller ones so that each only makes a single type of change. If this is not possible, select all the *Change Type* emojis that are applicable. Use whitespace in accordance with your personal preference. Complete the rest of the message with whatever issue number and description you normally would include. 

Applying the emojis on a Macbook is very quick as you simply need to press ctrl + cmd + space to bring up the emoji selector. I prefer to add and group those emojis used above in my favourites so it is even easier to chose from among them. You can copy and paste the emojis into the search bar on the emoji picker to find them for the first time.

There are likely to be similar processes on other operating systems.

## Benefits

The descriptive benefits of using a system like the emoji one described above are obvious. It provides high-level information about the type of changes being made and the nature of the work developers are doing - without meaningfully impacting developers’ ability to add short summaries of their changes. It also provides a useful overview during code review and to product management.

Through manual or automated analysis it is possible to establish an indicator of progress based on empirical data that is inexpensive to record. Such an analysis may be extended to not only individual commits, but sequences of them to highlight potential problems. A series of new feature commits absent a documentation commit may be cause to follow up on ensuring the new feature is adequately documented. A bugfix commit without any subsequent code review may suggest checking the review logs. Similarly, a new project without a lint commit may indicate the development environment may require further attention. These can be encoded and automated in the analysis tool of your choice, to raise warnings at the points in your workflow that make the most sense.

The real benefits, however, are when such a system is used prescriptively to encourage better code changes while they are being written. If a developer writes a commit messages before starting coding, it encourages them to carefully consider the changes they are about to make ahead of time. This process of thinking through the new feature or fix often leads to insights and allows recognising dead ends in advance, saving the time otherwise lost following them. Selecting the approriate emojis forces thinking in terms of the type of work to be done, keeping the developer focused and highlighting the testing and documentation that should be performed at each step.

I personally like to place the commit message somewhere on-screen that is always visible as it allows me to periodically check that I have not strayed from my original intent. It doesn't mean a commit's emojis or message cannot be changed, but it gives me something to work towards. I cannot suddenly start performing a different type of work, or begin working on another part of the system without realising it, because I see that I must change the emoji to match the changes being made and take pause.

## Potential problems

There is the obvious disadvantage of emojis being represented differently across operating systems. However, although they may appear differently, the meaning assigned above is attached to the emoji's specification (a text description) and not necessarily the visual image. So if the colour, style, angle or other details of the emoji are different, the core concept remain constant. If you are part of a team that uses different environments and tools, you may wish to consider whether this is likely to be a problem for you.

Rendering emojis may also present a problem for some git clients (although I have not encountered any).


### Related articles

- [10 things good front-end developers do](/blog/2016/07/21/10-things-good-front-end-developers-do)
