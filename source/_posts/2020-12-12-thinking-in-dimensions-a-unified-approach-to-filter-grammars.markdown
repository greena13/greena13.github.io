---
layout: post
title: "Thinking in Dimensions: A Unified Approach to Filter grammars"
date: 2020-12-12 14:48:12 +0000
comments: true
categories: 
---

Learning filter grammars (such as CSS selectors, regular expressions or SQL) can be slow and error prone. Cheatsheets help with picking up a new grammar, or revising an old one you may be returning to after a period of absence, but because each has different concepts, syntax and operators, it's often difficult to see the similarities. They are so different that it's easy to lose track of the fact that they are all different tools to perform the same job.

This article explains a unified approach to filter grammars that provides a framework to classify, select and apply them appropriately; it also aids to reduce the barrier to identifying and learning new ones when your application calls for it. 

<!--more-->

## General terminology: Dimensions and Nodes

To understand a filter grammar, you must first understand the dimensions or properties of the data structure it is designed for. The term _dimension_ has its limitations (I use it because I find it helpful to think of these properties visually in dimensional space, but you may prefer instead think of them as simply attributes, or members of a tuple). 

Filter grammars tend to operate on and return results of the same type, which can be thought of as the _nodes_ of the data structure. It doesn't matter what logical structure the data takes (whether that's a HTML document tree, or a flat list of file lines), it's useful to think of the data being filtering as a series of independent nodes, or points in space you arrive at by matching on each dimension's value.

## Semantic dimensions 

Identifying what _semantic_ dimensions a data structure has is a process of considering its logic structure and meaning, and examining what specialised operators the filter grammar provides to reflect that meaning. 

We'll take HTML documents as an convenient example as it is used with common filter grammars like CSS and XPath. 

HTML documents have a number of _semantic_ dimensions:

* An element's _hierarchy_ (it's relationship to its ancestors and descants)
* An element's siblings (its grouping with other elements that share a parent element) and often more importantly, it's _position_ within those siblings.
* An element's _type_ (the tag type it's given in the document)
* An element's _attributes_
* An element's _text content_
* An element's _states_ (focused, hovered, disabled, etc)

A filter grammar won't always provide the ability to filter on all of these dimensions. For example, in CSS you can apply filters on an elements' hierarchy, position, type and attribute, but not really its text content (for that you need XPath or possible jQuery). And XPath does not provide the ability to filter on an element's state, but CSS does.

### Filter grammars are comprised of smaller ones

Semantic dimensions are typically independent and so filter grammars can be thought of as a collection of _smaller_, simpler grammars, applied to specific dimensions. Although there are cases in some filter grammars where two or more properties are combined when matched, the dimensions tend to be kept separate. Which of dimension to filter, is usually indicated with some sort of switch. For example, in CSS, the period indicator (`.`) proceeds matches that should be applied to an element's `class` attribute, and a hash (`#`) is used to match on an element's `id`.

## Data types

Once you have identified the semantic dimensions of a data structure, you need to determine the data type the filter grammar treats it as:

* Nominal: Named categories (e.g. enums, booleans). These values have no magnitude (no one value is greater than any other), no intervals (they're discrete categories with no values in between) and no absolute values (each value has meaning only in it being distinct from the other possible values and not some global measure).
* Ordinal: Discrete values (e.g. integers, hierarchy, position) that have an explicit ordering amongst values. They have magnitude (values can be ordered from smallest to largest), but no "in between" values.
* Scalar: Continuous values (e.g. floats). They are like ordinals, but have an infinite precision (up to the limitations of the underlying datatype).
 
From these, there are some special cases:

* Sets and fields: Sets or collections where the order of elements does not matter. This is usually a way of encoding nominal data. (e.g. comma or space separated lists of words)  
* Permutations: Sets or collections, where the elements are in a specific order (e.g. arrays, characters in a string, hierarchies, file paths and list items). The order itself is ordinal data (i.e. the first position - index 0 - is always lower or before the second position - index 1 - and so on).

A dimension's data type determines which operators are possible (and their semantics).

### Nominal data

The simplest dimensional representation to visualise is the number line. Similarly, the simplest operator is the _presence_ one - which determines whether there is a value at all. After that, we consider the _equality_ operator by placing values on the number line, and evaluating if they occupy the same position. We can then consider their complements - the _not present_ and _not equal_ operators. It is at this point we have effectively explored the full set of basic operators available for nominal data.
          
### Ordinal data

Ordinal data (which adds magnitude) allows us to consider values _relative_ to a reference points, often termed the _anchor_. We can compare two values relative to one another by deeming one the anchor and evaluating if a value is closer or further away to one end of the number line. This is how we get the _less than_ and _greater than_ operators (which we can overlap with the equality operators to get _less than or equal to_ and _greater than or equal to_). These operators are complements of one another (paying special attention to where you want the equality to fall: e.g. the complement of _less than_ is not _greater than_ but _greater than or equal to_.) 

If you place the anchor at the first or last possible value of a dimension (often termed a _global anchor_) in order to find its closest neighbor, you achieve the _minimum_ and _maximum_ operators. 

If you introduce an additional anchor (which is the same as applying the _less than_ and _greater than_ operators one after another), it's possible to identify if a value is _between_ two others. You can also place limits on the _less than_ and _greater than_ operators, to get the _within_ operator (requiring a value to be within a certain number of places of another).
    
### Scalar data

Scalar values - which not only have magnitude, but are continuous and have equal intervals as well - allow  aggregate functions such as averages and percentiles which may yield results between the original values (more on this in a minute).

### Permutation data

Stepping away from the number line, and instead considering permutations, we get a different set of semantics for the same operators: Anchors are not based on values but position (e.g. at the start of the permutation, or at the end).

With the number line, the position of the values were determined by those values themselves. However, permutations have both the position of the value, and the value itself. For example, in the string "Monday", 'n' has position 2 (0 indexed) and value 'n'. Some operators set or anchor the position and attempt to match the value, like _begins with_ or _ends with_. These are the global anchors. However, some operators require relative offsets or ranges for the position value, like the _proceeded by_ operator; these are the relative anchors. Global anchors match on the position and then value and relative anchors match on value and then position, so neither one dimension is inherently more important than the other.
         
### Summary of semantic dimension operators

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 96px;"/>
        <col style="width: 137px;"/>
        <col style="width: 118px;"/>
        <col style="width: 129px;"/>
        <col style="width: 154px;"/>
        <col style="width: 181px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 137px; padding: 8px;">
            <div>Nominal</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 118px; padding: 8px;">
            <div>Sets and fields</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 129px; padding: 8px;">
            <div>Ordinal</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 154px; padding: 8px;">
            <div>Scalar</div>
        </td>
        <td style="vertical-align: bottom; border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Permutations</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Magnitude</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 255px; padding: 8px;">
            <div>None</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 129px; padding: 8px;">
            <div>Yes</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 154px; padding: 8px;">
            <div>Yes</div>
        </td>
        <td style="vertical-align: bottom; border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Yes. (Ordinal value of positions)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Discrete</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 255px; padding: 8px;">
            <div>Yes</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 129px; padding: 8px;">
            <div>Yes</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 154px; padding: 8px;">
            <div>No. (Continuous.)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Yes (Each position has discrete index)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Absolute values</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 255px; padding: 8px;">
            <div>No</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 129px; padding: 8px;">
            <div>Yes.</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 154px; padding: 8px;">
            <div>Yes.</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Yes. (First, last)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Examples</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 137px; padding: 8px;">
            <div>Boolean, Categories</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 118px; padding: 8px;">
            <div>Comma or space separated list of words</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 129px; padding: 8px;">
            <div>Integers</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 154px; padding: 8px;">
            <div>Floats</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Strings, Hierarchy, Lists</div>
        </td>
    </tr>
    <tr>
        <td colspan="6"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 815px; padding: 8px;">
            <div>Non anchored match</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Any/presence</div>
        </td>
        <td colspan="5" style="border: 1px solid rgb(204, 204, 204); width: 719px; padding: 8px;">
            <div>Presence (not undefined)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Exact match</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 137px; padding: 8px;">
            <div>Equals</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 118px; padding: 8px;">
            <div>Field is equal</div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 464px; padding: 8px;">
            <div>Equals</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Partial match</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 137px; padding: 8px;">
            <div><span
                    style="color: rgb(134, 134, 134); --inversion-type-color:  simple;">Not applicable</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 118px; padding: 8px;">
            <div>Set contains element</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 283px; padding: 8px;">
            <div><span
                    style="color: rgb(134, 134, 134); --inversion-type-color:  simple;">Not applicable</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Contains (smaller permutation)</div>
        </td>
    </tr>
    <tr>
        <td colspan="6"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 815px; padding: 8px;">
            <div><span style="color: rgb(0, 0, 0); --inversion-type-color:  simple;">Top/Left anchored</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Absolute match</div>
        </td>
        <td rowspan="4"
            style="height: 128px; border: 1px solid rgb(204, 204, 204); width: 255px; padding: 8px;"
            colspan="2">
            <div><span style="color: rgb(134, 134, 134); --inversion-type-color:  simple;">Not applicable - no position or magnitude, so anchors have no meaning</span>
            </div>
            <div><br/></div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 283px; padding: 8px;">
            <div>Lowest (equal to lowest value in the set)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>First, Begins with (Matches elements in first positions)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Immediate relative match</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 283px; padding: 8px;">
            <div>Immediately after (value in set immediately after anchor)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Immediately followed by (matches element immediately before anchor)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Any relative match</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 283px; padding: 8px;">
            <div>More than (one of the values after the anchor)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Immediately followed by (matches any element before the anchor)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Scoped relative match</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 283px; padding: 8px;">
            <div>Within (one of the values less than the anchor, within a certain number of places)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Separated by (set or number of possible values)</div>
        </td>
    </tr>
    <tr>
        <td colspan="6"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(204, 204, 204); width: 815px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Bottom/Right anchored</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Absolute match</div>
        </td>
        <td rowspan="4"
            style="height: 128px; border: 1px solid rgb(204, 204, 204); width: 255px; padding: 8px;"
            colspan="2">
            <div><span style="color: rgb(134, 134, 134); --inversion-type-color:  simple;">Not applicable - no position or magnitude, so anchors have no meaning</span>
            </div>
            <div><br/></div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 283px; padding: 8px;">
            <div>Highest</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Last, Ends with</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Immediate relative match</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 283px; padding: 8px;">
            <div>Immediately before (value in set immediately before anchor)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Immediately proceeded by</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Any relative match</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 283px; padding: 8px;">
            <div>Less than (one of the values before the anchor)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Proceeded by</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 96px; padding: 8px;">
            <div>Scoped relative match</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 283px; padding: 8px;">
            <div>Within (one of the values more than the anchor, within a certain number of places)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 181px; padding: 8px;">
            <div>Separated by (set or number of possible values)</div>
        </td>
    </tr>
    </tbody>
</table>

## Other dimension types

We've discussed _semantic dimensions_ thus far, but there are at least two other types.

The first is revealed by the use of _projections_. This can also be thought of as projecting values into another dimension (using some function or transform), which has its own applicable operators. Aggregate functions are an example of a projection; they provide operators that do not depend on the values themselves, but consider the value in context with the rest of those in the set. For example, the _most common_ operator, creates _count_ data for each value (grouping those that are the same) and then applies a _maximum_ operator.  Projections often production dimensions that are a different data type than their input values.

Another dimension type is revealed by examining the _underlying data type_ being used to represent a semantic dimension. For example, nominal data or ordinal data values may be represented as strings, which are themselves permutations of characters and the filter grammar may provide access to these operators.

For example, consider the set of the days of the week: 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'. To match on these as ordinal values, we could query the "Days before Wednesday", but we could also query them on the positions of the characters in each string by asking for "The values starting with 'T'".

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 202px;"/>
        <col style="width: 597px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 202px; padding: 8px;">
            <div>Semantic dimension</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 597px; padding: 8px;">
            <div>Inherit in the semantics of the data structure and filter grammar</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 202px; padding: 8px;">
            <div>Data type dimensions</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 597px; padding: 8px;">
            <div>Inherit in the type of data used to represent the values of the semantic dimensions</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 202px; padding: 8px;">
            <div>Aggregate functions and transforms</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 597px; padding: 8px;">
            <div>Achieved by applying functions to values and projecting them into a different dimension,
                which has it own operators
            </div>
        </td>
    </tr>
    </tbody>
</table>
          
## The relationship between dimensions and operator complexity
           
There appears to be roughly a continuum of filter grammars and data structures. At one end are the data structures with many semantic dimensions, with filter grammars that have relatively few projection or data dimension operators. One such example is CSS - the data structure (HTML documents) have many semantic dimensions and CSS selectors are largely defined in terms of these semantic dimensions, with restricted data and projection dimension operators. You cannot for example, find all elements with tags that _start with_ a sequence of characters, or find _the most common_ element. 

In contrast, XPath includes a lot less of the semantic dimensions CSS is designed around, and instead treats a HTML document as a generic tree structure. Consequently, it has richer set of data type dimension operators, providing a lot more power (at the cost of increased expression complexity and reduced performance). 

At the other end of the continuum are regular expressions, which operate on flat strings (usually single lines in a file) which have few semantic dimensions. Instead there is only the data type's dimensions, which the filter grammar provides very complex operators for (well beyond those we've discussed so far). If applied to a HTML document, it interprets it only as a line semantic dimension (a permutation of characters which have the two data dimensions of position and character value), requiring a complex sequence of expressions in order to match the same nodes that would be trivial in CSS. However, it's possible to match character sequences that are outside of valid HTML, or which are normalized away such as whitespace. 

Utilities like `sed` and `awk` (which use regular expressions to match lines) add a few more semantic dimensions by providing the ability to match on line numbers and ranges. `awk` also separates lines into fields and provides projection dimensions by offering the ability to generate mathematical aggregate calculations like _count_, _average_ etc. 

## Combining expressions

Putting dimensions aside now, the other component to filter grammars are how expressions are combined. 

There are 4 main logic operators:

* AND: Both expressions must be true
* OR: One or both of the expressions must be true
* NOT: The expression must be false, for its complement to return true
* Exclusive OR (XOR): One or the other of the expressions must be true, but not both

Depending on the grammar, these operators may be available outside of expressions, to operate on them as a whole expression, or within expressions to operate on specific dimensions, or both.

For example, in CSS to find `div`s with the class `foo` and `bar`, you apply the operator within the expression:

```css
div.foo.bar
```

But to find divs with a class `foo` or `bar`, you must combine separate expressions:

```css
div.foo, div.bar
```

XPath on the other hand, provides the AND and OR operators both inside and outside an expression:
                       
The following are equivalent AND expressions:

```
//div[@class="foo"][@class="bar"]
//div[@class="foo" and @class="bar"]
```
      
And the following are equivalent OR expressions:

```
//div[@class="foo"] | //div[@class="bar"]
//div[@class="foo" or @class="bar"]
```

## Filtering vs Selection

For some filter grammars, filtering and selection are usually the same operation (the nodes that match the filter are the ones that are selected), with a few operators that are the exception. This is true of CSS: the elements returned are those matched by the filtering operators, with the exception of the pseudo-selectors (those starting with a colon) - these continue to filter without changing the selection target to a child or sibling. 

```css
ul > li:first-child
```

There is, however, no way to select an element based on its descendants without changing the selection focus _to_ those descendants. The same is not true of XPath expressions, which always selecting the nodes that match the filter, but also allow filtering nodes based on their ancestors or children without changing the selection target.

```
# Selects descendant
//ul/li[1]

# Select parent based on descendant
//ul[li[position()=1]]
```

Some grammars require explicitly specifying what you want to select from the nodes that match the filter, like SQL's `SELECT` operator. 

```sql
-- Select username from users table
SELECT username FROM  users;
```

Regular expressions are perhaps the most complicated, with their _capturing groups_ and _non-capturing groups_ like the _look ahead_ and _look behind_ operators. 

```
# Select "foo" followed by "bar" (but don't include "bar" in the output)
/(foo)(?=bar)*/
```

`awk` also provides a _hold space_ that allows for having the target of the selection be different from the target for filtering.

A in-depth discussion of each is outside the scope of this article, but it is worth noting many grammars provide a slightly different solution to the same problem.
                          
## Overview of common filtering grammars

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 126px;"/>
        <col style="width: 111px;"/>
        <col style="width: 182px;"/>
        <col style="width: 184px;"/>
        <col style="width: 208px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 126px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="vertical-align: bottom; border: 1px solid rgb(204, 204, 204); width: 111px; padding: 8px;">
            <div>Operate on</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>Semantic Dimensions</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Type</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 208px; padding: 8px;">
            <div>Selection</div>
        </td>
    </tr>
    <tr>
        <td rowspan="5"
            style="height: 164px; border: 1px solid rgb(204, 204, 204); width: 126px; padding: 8px;">
            <div>CSS Selectors</div>
        </td>
        <td rowspan="10"
            style="height: 344px; border: 1px solid rgb(204, 204, 204); width: 111px; padding: 8px;">
            <div>HTML or XML documents</div>
            <div><br/></div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>Element hierarchy</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Permutation</div>
        </td>
        <td rowspan="5"
            style="height: 164px; border: 1px solid rgb(204, 204, 204); width: 208px; padding: 8px;">
            <div>Pseudo-selectors further filter nodes without changing selection target</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>Element position</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Permutation</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>Element type</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Nominal</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>Attribute values</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>id and class: Space-separated Fields</div>
            <div>Permutation (String)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>Element’s state (hovered, focused, etc)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Nominal</div>
        </td>
    </tr>
    <tr>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 126px; padding: 8px;">
            <div>XPath</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 366px; padding: 8px;">
            <div>Same as CSS selectors</div>
            <div>- Element’s state</div>
        </td>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 208px; padding: 8px;">
            <div>Use filtering on descendants or ancestors to filter without changing selection target</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>+ Element’s text content</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Permutation (String)</div>
        </td>
    </tr>
    <tr>
        <td rowspan="3"
            style="height: 92px; border: 1px solid rgb(204, 204, 204); width: 126px; padding: 8px;">
            <div>jQuery Selectors</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 366px; padding: 8px;">
            <div>Same as CSS selector</div>
        </td>
        <td rowspan="3"
            style="height: 92px; border: 1px solid rgb(204, 204, 204); width: 208px; padding: 8px;">
            <div>Can use DOM traversal (outside of selection expression)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>+ Some internal state (animation)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Nominal</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>+ Some extra operators for those dimensions</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 126px; padding: 8px;">
            <div>Globbing</div>
        </td>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 111px; padding: 8px;">
            <div>File paths</div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>Directory hierarchy</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Permutation</div>
        </td>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 208px; padding: 8px;">
            <div>Can use parent operator to select siblings or ancestors of match</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>Directory and file names</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Permutation (String)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 126px; padding: 8px;">
            <div>Regular Expressions</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 111px; padding: 8px;">
            <div>Strings</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>String content</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Permutation (String)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 208px; padding: 8px;">
            <div>Use capturing groups, look aheads and look behinds</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 126px; padding: 8px;">
            <div>grep</div>
        </td>
        <td style="height: 164px; border: 1px solid rgb(204, 204, 204); width: 111px; padding: 8px;"
            rowspan="5">
            <div>File contents</div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>File lines</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Permutation (String)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 208px; padding: 8px;">
            <div>Use arguments to return files or lines</div>
        </td>
    </tr>
    <tr>
        <td style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 126px; padding: 8px;"
            rowspan="2">
            <div>sed</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 366px; padding: 8px;">
            <div>Same as <span style="font-family: &quot;Courier New&quot;;">grep</span> </div>
        </td>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 208px; padding: 8px;">
            <div>Use arguments to decide what’s printed</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>+ Line position</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Permutation</div>
        </td>
    </tr>
    <tr>
        <td style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 126px; padding: 8px;"
            rowspan="2">
            <div>awk</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 366px; padding: 8px;">
            <div>Same as <span style="font-family: &quot;Courier New&quot;;">sed</span></div>
        </td>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 208px; padding: 8px;">
            <div>Use print to decide output</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>+ Fields</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Fields</div>
        </td>
    </tr>
    <tr>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 126px; padding: 8px;">
            <div>SQL Filtering &amp; Selection</div>
        </td>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 111px; padding: 8px;">
            <div>Relational databases</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>Tables</div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Nominal</div>
        </td>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 208px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">SELECT</span> operator</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 182px; padding: 8px;">
            <div>Fields</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Depends on data type</div>
        </td>
    </tr>
    </tbody>
</table>

## Examples

* [CSS Selectors Cheatsheet](/blog/2020/12/15/css-selectors-cheatsheet).
* [XPath Selectors Cheatsheet](/blog/2020/12/15/xpath-selectors-cheatsheet).
