---
layout: post
title: "Facebook Origami Cheat Sheet"
date: 2016-06-30 13:36:51 +0100
comments: true
categories: Facebook Origami cheat sheet
---

[Facebook's Origami](http://facebook.github.io/origami/) is an interesting UI prototyping tool that integrates nicely with [Sketch](https://www.sketchapp.com/) and offers the ability to interact with your prototypes in a simulator or right on your mobile or tablet device.

This is a summary of the information I picked up from watching the [Origami video tutorials](http://facebook.github.io/origami/tutorials/). It's not a complete reference, but it succinctly captures most of the information contained in the video and can serve as a quick reference.

First, a note on terminology: as I am still learning my way around the Origami and Quartz Composer nomenclature, I may have incorrectly used some of the terminology concerned with what each of the components or widgets are called. I have also introduced groupings of layers and patches by function that were not suggested in the tutorials, but added clarity for me. They may, however, not necessarily correspond with discussion you read elsewhere.

## Overview

Origami has two main concepts: patches and layers. Layers control what is rendered on the screen and can be thought of as the outputs of the prototype you are building. They take their input values from patches, which contain the logic of the prototype. And the patches take *their* input from user actions either on the simulator or the device that is running the prototype.

You connect patches and layers together by dragging lines between the inputs and outputs of components, in a fashion that is analogous to wiring an electrical circuit, or creating a flow chart.

So, the user interacts with the prototype, by scrolling, tapping or some other gesture. This gets relayed to your canvas of components using an interaction patch (discussed later) which represents the event as a binary value (1 when it's happening, otherwise 0) and you are free to perform any number of operations on this signal, like scale it, remap it, combine it, delay it or shape it, by connecting different transform patches together. When you are done, you should have a signal that can control one or more properties of the layers, like alpha, position, or size.

This is best represented visually, so it's probably worth watching the [first video tutorial](http://facebook.github.io/origami/tutorials/) to get a feel for what this looks like. Once you have watched the first video, however, the remaining videos are more or less the same thing, using different patches. You can find a catalogue of the patches used in the videos below.

## Layers

Layers describe how each content gets rendered on the device, by setting properties like:

- position
- dimensions
- rotation
- image/mask image
- colour
- opacity
- scale

### Fixed Layer Components

These components can't be changed by user interaction and represent constant properties of the simulation.

**Viewer**

Represents the output of the prototype, or the simulated device.

**Viewer Size**

Allows changing the simulated device (mobile devices, tablets or watch) and determines the dimensions of the layer group (see below).

### Dynamic Layer Components

These components receive input signals from patches to control their values and are used to create the interactivity of the prototype.

| Key | Description |
| :-------------: | :------------- |
| G | Create layer group from selected |
| Cmd + [, Cmd + ] | Increment or decrement a selected layer's layer number |
| Right click | Quick alignment options |

**Layer Group**

Layer groups contain the things that will appear on the screen. They have square corners to distinguish them from non-groups. You enter a layer group by double clicking on them and exit them by clicking the up caret button in the top right of the screen.

**Image Patch**

The easiest way to add an asset is by dragging the image from Finder into the layer group on the screen and Origami will create an image patch and assigns a layer to it, which allows you to customise how it appears.

**Fill Layer**

Fill layers allow you to specify the colour and opacity of a fill.

**Text Layer**

Text layers displays font on screen and provide properties for changing position and styling.

## Patch creation

Patches are created by using the corresponding keyboard shortcut or bringing up the patch list dialogue and either searching or clicking on the patch that you are looking for. Be careful not to select patches outside of the Origami package as some have very similar names to Origami ones.

| Key | Patch |
| :-------------: | :------------- |
| Cmd + Enter | Patch list dialogue |
| T | Transition |
| I | Interaction 2 |
| A | Pop animation |
| L | Layer |
| C | Conditional |
| D | Delay |
| W | Wireless |
| Shift + S | Switch |
| Shift + A | Logic |
| R | Reverse progress patch under cursor |
| Alt + Click & Drag | Duplicate patch |

By hovering the cursor over a port and pressing the appropriate keyboard shortcut for the patch you want, the patch is automatically created and named based on the port that it is connected to.


## Patch Types

### User Interaction

User interaction patches don't really have input ports and instead listen for events that occur when the user interacts with the prototype. They produce a signal on their output ports when a user event occurs that matches the patches type and options.

**Interaction 2 Patch**

Converts the user's simulated gestures to 1 or 0.

| Option | Output Description |
| :------------- | :------------- |
| down | 1 if finger held down, else 0 |
| up | 1 for a single frame following a finger release, else 0 |
| tap | 1 when finger is released from the screen and the finger is stationary, else 0 |
| drag | 1 when finger goes down to drag and remains 1 while dragging, else 0 |


**Scroll Patch**

Outputs 1 when a user scrolls an area.

**Swipe Patch**

Outputs 1 when a user swipes on an area.

The **jump to start** option is used for programatically triggering a swipe action, which is useful for back buttons and those that trigger swipe transitions.

*Beware not to get the non-Origami one of the same name*.

**Hit Area Patch**

Limits the user input captured to a particular area of screen. You need to connect the hit area patch to the interaction patch via the interaction port at the top of the patch, to the right of the title.

### Transforms

These patches are used to modify and combine the signals from the interaction patches. They are like the components of a circuit: the resistors, capacitors, transistors and transformers.

### Binary patches

These are patches that output binary values: 0 or 1.

**Logic Patch**

These allow you to combine to signals in a logic operation. Invaluable when you need 2 things to be happening at once for a transition to occur, or to disable a behaviour if another is currently occurring.

**Conditional Patch**

Outputs 1 when the input signal satisfies a given constraint. Useful for waiting for a given threshold to be exceeded before setting off an event.

**Switch Patch**

Like a switch, it remembers and maintains its own internal state and uses its input signal to decide when to toggle that state. Useful for remembering if a user has already done something and triggering upstream events, accordingly.

There is a convention of naming these patches according to what the *on* state represents.

### Digital patches

These are patches that output continuous data and are not limited to 0 or 1.

**Transition Patch**

Takes input between 0 and 1 and proportionally outputs a value within a different range. For example, for an output range between 1 and 10:

| Input | Output |
| :------------- | :------------- |
| 0 | 1 |
| 0.5 | 5 |
| 1 | 10 |

**Pop Animation Patch**

Does the same thing as a transition patch, but using a more complex relationship. You can specify the bounciness and speed of the transition, so the equivalent value for a given input is not immediately available on the output. This is an effective way of converting a binary signal to an a digital one.

**Progress Patch**

A progress patch does the reverse of the transition patch: takes a range of input values and outputs value between 0 and 1.

**Reverse Progress Patch**

A reverse progress patch complements the signal of a progress patch it is plugged into. This is great for representing inverse relationships - say you want one component to fade in as another is fading out.

**Range Patch**

A range patch limits the possible set of values it receives. It outputs a clipped value and a rolled over value (how much the input has exceeded the specified range). This is good if you want to, have a widget change position relative to an input value, but limit its movement so it cannot go off the screen.

**Delay Patch**

Delay patches output their input signals after a specified delay. Useful for delaying animation and sequencing events.

# Keeping your canvas organised

Patches and layers can be placed into layer groups in the same way files can be placed in directories to keep them organised. You can nest layer groups as deep as you like and give them useful names to help you navigate. It is useful to contain different screens and widgets in their own layer groups to keep them contained.

You can select patches and layers and cut and paste them inside your layer groups as you go along, or you can drag selected components on top of a layer group and they will be moved inside.

## Connecting patches

| Key | Description |
| :------------- | :------------- |
| option + drag | Drag from last selected input/output |

You can make a patch’s output port available as input for patches contained inside a layer group by dragging the output port onto the layer group and giving it a name in the dialogue that appears.

The reverse is possible as well: you can make a patch’s output available outside of the layer group that contains it by doing one of the following:

### Passing it up one layer at a time

- Hover the cursor over the output port and press P and name the port
- Navigate up one level
- Connect another patch to the new output port that has appeared on the layer group


### Use a Wireless Patch

- Press W with the cursor hovered over the patch's output port
- Click and rename the new wireless patch that appears on the canvas
- Navigate up as many levels as you need
- Press W while over the input port of the patch you wish to connect to OR shift + W with the cursor over the canvas (and not over a patch)
- Select the wireless patch that appears and press Cmd + 2 to go into the settings panel and select the name of the wireless patch


## Values

You can long click on any value field to get a slider rather trying to calibrate numerical values to get what you are looking for.
