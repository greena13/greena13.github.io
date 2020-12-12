---
layout: post
title: "SourceTree Keyboard Shortcuts"
date: 2015-02-01 09:32:42 +0000
comments: true
categories: ["Keyboard Shortcuts", "Efficiency", "SourceTree", "Atlassian", "Version Control"]
---

Originally (back in 2015) this post was a record of my attempts to brute force the discovery of Sourcetree keyboard shortcuts, because they were not well-documented by Atlassian (or anyone else I could find at the time).

Now, in 2020, the keyboard shortcuts are readily available both in-program (on each context menu item) as well as a few easily located resources online. So I'll repurpose this blog post instead to surface some of the more _useful_ keyboard shortcuts (this list is no longer exhaustive), and explain how I _extend_ the keyboard shortcuts Sourcetree provides natively. 

<!--more-->

These are the Mac keyboard shortcuts (although I suspect the Windows equivalents are more often than not, the same but with ctrl instead of cmd).

## Navigation \& Menus

| __Action__  | __Shortcut__ |
|:--------|---------:|
| __File Status Screen__ | cmd + 1 |
| __History Screen__ | cmd + 2 |
| __Search Screen__ | cmd + 3 |
| __New repo__  |  cmd + n  |
| __Hide/show sidebar__  |  cmd + shift + k |
| __Repository details__  |  cmd + shift + i |
| __Open file browser (fastest way to switch repositories)__  |  cmd + o |

## Staging \& Commit Operations

| __Action__  | __Shortcut__ |
|:--------|---------:|
| __Commit All... Screen__  |  cmd + shift + c |
| __Commit options__  |  cmd + shift + o |
| __Reset Selected File__ |  cmd + shift + r |
| __Diff__  |  cmd + d  |
| __Remove Selected File__ | cmd + delete |
| __Search__  |  cmd + shift + h |
| __Refresh__  |  cmd + r  |

## Removing and resetting files

| __Action__  | __Shortcut__ |
|:--------|---------:|
| __Delete__  |  cmd + delete  |
| __Reset file to staged version__  |  cmd + alt + R  |

## Find

| __Action__  | __Shortcut__ |
|:--------|---------:|
| __Find (file by name or path)__  |  cmd + f  |
| __Find Next__  |  cmd + g  |
| __Find Previous__  |  cmd + shit + g  |
| __Use selection to find__  |  cmd + e  |

## Branch  Operations

| __Action__  | __Shortcut__ |
|:--------|---------:|
| __Stash__  |  cmd + shift + s |
| __Add or Remove Tag__  |  cmd + shift + t |
| __Branch__  |  cmd + shift + b |
| __Merge__  |  cmd + shift + m |

## Repository Operations

| __Action__  | __Shortcut__ |
|:--------|---------:|
| __Fetch__  |  cmd + shift + f |
| __Pull from repository__  |  cmd + shift + l |
| __Open remote in web browser__  |  cmd + shift + w |
| __Open pull request__  |  cmd + alt + p |

## Window (Normal Mac Shortcuts)

| __Action__  | __Shortcut__ |
|:--------|---------:|
| __Quit__  |  cmd + q  |
| __Close window__  |  cmd + w  |
| __Hide__  |  cmd + h  |
| __Minimise__  |  cmd + m  |
| __Settings__  |  cmd + ,  |
| __Undo__ | Cmd + z |
| __Select All__ | Cmd + a |
| __Emoji & Synbols__ | Cmd + ctrl + space |

## Creating your own keyboard shortcuts

Sourcetree is no different than any other Mac application, in that you can define your own keyboard shortcuts for system menu items.

Identify the menu item that either has no keyboard shortcut, or one that you don't like, and take note of its name.

Open System Preferences, select the *Keyboard* option, then the *Shortcuts* tab, followed by the *App Shortcuts* menu item. Click the + button, Select Sourcetree as the *Application*, your menu name as *Menu Title* and define your keyboard shortcut. Close System Preferences and reopen Sourcetree to use.

## Going beyond pre-defined actions and navigating menu items

I use [Vimac](https://vimacapp.com/) to navigate the parts of the Sourcetree UI and perform actions that do not have convenient keyboard shortcuts or menu items.

It provides the following keyboard shortcuts (each is configurable):

| __Action__  | __Shortcut__ |
|:--------|---------:|
| __Activate__  |  ctrl + space |
| __Right click__  |  Hold shift when typing macro |
| __Double click__  |  Hold cmd when typing macro |

There are alternatives such as [Keyboard Maestro](https://www.keyboardmaestro.com/main/) (Paid software) that may be better suited to your preferences.

### Related

- [Facebook Origami Cheat Sheet](/blog/2016/06/30/facebook-origami-cheat-sheet)
