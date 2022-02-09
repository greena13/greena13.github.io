---
layout: post
title: "Style your design elements, NOT your mark up"
date: 2022-02-07 19:13:21 +0000
comments: true
categories: 
---

A common mistake made when developing digital products is to express their designs in terms of the underlying markup or primitives of the technology being used. This is like decorating a room by painting each raw material, instead of by wall, floor or fixture. 

<!--more-->

## Content vs Chrome

Content is the actual _stuff_ your user is trying to access or interact with, whether that be text, images, multimedia or something else. Without it, there is no reason for your app or website to exist at all.

[Chrome](https://www.nngroup.com/articles/browser-and-gui-chrome/#:~:text=Definition%3A%20Chrome%20is%20the%20visual,being%20part%20of%20that%20content) is everything that's _not_ the content: it's all the UI your app or website provides to navigate, search, paginate, summarise, or interact with the content. It's the buttons, links, menus, widgets, and other UI elements your product includes. Without it, your content is a static, indiscernible blob that can't be easily navigated, interacted with, or understood.

## Markup vs Design Elements

_Content_ can be expressed in any format you like: JSON, HTML and XML are common examples. The latter two are markup languages, which allow expressing the semantic type, and structural relationship between data. 

HTML also lets you express _UI Chrome_ as buttons, links and similar HTML tags. Analogous information has started to be included in JSON specifications such as [OpenAPI](https://swagger.io/docs/specification/links/) and [JSON:API](https://jsonapi.org/format/#document-links) as `links` and similar attributes. Although it's a stretch to deem this relationship information as UI Chrome (a JSON payload _has_ no UI), it's useful to note this concept may have a presence across formats (particularly when tasked with converting a document of one format from another).

Getting the semantic and structural markup correct in the a HTML document is essential, as it serves as the contract used by the the rest of the internet to understand what content (and ways of acting on that content) you have to offer. Search engine crawlers use it to index websites (which can be augmented through the use of standards like [OpenGraph](https://ogp.me/) or [Schema.org](https://schema.org)). Browsers use it to provide the correct default behaviour and screen readers rely on it to convert your content to audio, navigate the page and select between available media types (correct usage of HTML tags is further augmented by [aria attributes](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA)).

Nothing has been designed in a HTML document (in the UI/UX sense): The data available, its schema, relationships, and its available actions are all _engineering_ decisions. It's encoded into a given standard (we've used HTML as an example), which is specific to that standard. 

_Design Elements_ are the components your app or website's design is expressed in, and are specific to the _design_, not the underlying markup or components the product may be built from.  To reuse the analogy at the start of this article, these are the constructs of a room useful for designing it: the walls, ceiling, floor, fixtures, furniture, etc. The underlying markup is the constituent raw materials: bricks, wood, tile, etc. Whether an app is built for the web or mobile is irrelevant: the design elements remain the same, just as what a room is built from does not change that it has the same basic components. 

There may appear to be instances of overlap between design elements and the underlying components or markup at first glance, but these are misleading. Take the HTML `<a>` or `<button>` tags. You may be tempted to conflate them with link or button elements in your design, but there are often cases where the correct semantics for a relationship or action require a particular HTML tag, but the opposite design element (e.g. a link that has been designed as a button). 

Examples of design elements are somewhat difficult to give, because they will depend entirely on your design (and don't necessarily share the same list of primitives like the underlying markup does), but they may include things like Links, Buttons, Alerts, Breadcrumbs, Menus, Progress Indicators, Lists, Sidebars, etc.

## Style your Design Elements

By separating design elements from the underlying technology, we can see two very distinct tasks emerge in the construction of digital products: correctly encoding the semantics of content and UI chrome, and applying design elements. These can indeed even be performed at different points in time, or by different developers.

While the task of encoding semantic information is governed by specific standards, expressing design elements is much more open, and is the subject of different competing ideas and paradigms - and your chosen technology to some extent; the important thing is that it is done in a way that acknowledges design elements as a distinct and primary concept.
                                                                            
### Using class names

We'll first look at how this is achieved without the use of any additional frameworks, again using HTML and CSS as our example (although the concepts apply generally, regardless of the techology you're using).

Once you've identified your design elements with your design team, you need to establish a way of labelling the HTML document in terms of them. This is what the [class attribute](https://www.w3schools.com/html/html_classes.asp) is intended for: to provide pointers that can be used in CSS.

You may once again be tempted to style the markup directly just using CSS selectors to "keep the markup and styling separate"; or it may strike you that giving HTML elements (possibly very similarly named) design element `class` values is tautology. This is a noble goal and may have worked in previous decades when there was a high level of parody between product designs and the underlying markup, but no more. It fails to appreciate the thing you are styling _isn't_ the markup, but is in fact the design elements.

You may also be tempted to choose `class` values that reflect the underlying data being rendered. This too is flawed - it's not the data types you are styling (it's - you guessed it - the design elements). If you need to express further information about an entity than is appropriate for HTML tags, you can use [data=* attributes](https://www.w3schools.com/tags/att_data-.asp) - don't put it in the `class`. In a well-designed application, many different data types are likely to be styled in terms of relatively few design elements. For example, a list of places may be styled the same as a list of people (as a "Vertical Scrolling List"). 
                                                             
Once you have labelled the document with design elements, you are free to express your design (i.e. styling rules) in terms of those elements. Doing so leads to fewer styling rules that closely resemble the design elements and language you're seeking to capture or encode.

### BEM
                 
BEM, when used judiciously, is consistent with the approach of using class names that represent design elements, so long as the class names used are in terms of your design elements. 

### Utility classes

[Tailwind](https://tailwindcss.com/), [Bootstrap](https://getbootstrap.com/) and others like them, use a system of utility classes (generic class names with associated pre-defined style rules for cross-cutting concerns: e.g. `.centered` or `.hidden`). This is _somewhat_ like breaking up design elements into their constituent style rules and applying them not through a stylesheet (where this relationship can be expressed more clearly), but in the HTML document itself. 

If you do use utility classes, it's recommended to find some sort of dynamic (pre-processor) solution to wrap up these constituent style rules into design elements, so they can be understood as design primitives and re-used (e.g. some of the features of SaSS, or runtime dynamic HTML generation using partials or templates).   
                                                 
## Suggested workflow

The following is a suggested workflow for producing websites or web apps in terms of design elements, however a similar approach can be applied for different technologies:

* Render your content
* Render your chrome
* Ensure both are semantically correct or consistent, using whatever tools are available in your chosen technology
* Segment or label things in terms of your design elements 
* Style those design elements

It doesn't really matter whether you follow this workflow literally, or figuratively, in terms of how you approach the problem. For illustrative purposes, we'll assume the literal sequencing of these steps.

You may find you need to add extra markup (adding nesting/group is a common one) to achieve the design elements you need; this isn't an indication of error, but a reality of the technology.
                         
### Rendering correct HTML markup for content

For single page applications, the first task is often going from a JSON payload (which is relatively light on semantic information) to the correct HTML. This step can obviously be skipped for applications that render the markup directly.

```json
{
  "avatarUrl": "https://example.com/users/1/avatar.png",
  "firstName": "Jack",
  "lastName": "Smith",
  "dateOfBirth": "01/01/1990"
}
```
            
Ensuring the correct HTML markup for the content:

```html
<div>
    <img src="https://example.com/users/1/avatar.png" alt="User avatar"/>

    <dl>
        <dt>
            First name
        </dt>
        <dd>
            Jack
        </dd>
        <dt>
            Last name
        </dt>
        <dd>
            Smith
        </dd>
        <dt>
            Date of birth
        </dt>
        <dd>
            <time datetime="1990-01-01">
                January 1st, 1990
            </time>
        </dd>
    </dl>
</div>
```
      
### Add any UI Chrome
    
The next step is to add the any UI chrome required. This includes both the HTML markup and any associated behaviour, particularly for single page applications where the changes to the DOM are performed without reloading the entire document.

```html
<div>
    <!-- ... -->
    
    <a href="https://example.com/users/1/edit">
        Edit
    </a>
</div>
```

### Augment the HTML with aria and schema tags

Once you have the basic HTML for the content and chrome, finish off your markup by adding the correct aria tags and [OpenGraph](https://ogp.me/) or [Schema.org](https://schema.org)) as appropriate (not shown here for brevity).

### Add your design elements

Once you have correct markup, you can add your design elements:                  

```html
<div class="list-item">
    <img src="https://example.com/users/1/avatar.png" alt="User avatar" class="list-item-avatar"/>

    <dl class="list-item-body">
        <dt>
            First name
        </dt>
        <dd>
            Jack
        </dd>
        <dt>
            Last name
        </dt>
        <dd>
            Smith
        </dd>
        <dt>
            Date of birth
        </dt>
        <dd>
            <time datetime="1990-01-01">
                January 1st, 1990
            </time>
        </dd>
    </dl>
    
    <a href="https://example.com/users/1/edit" class="button primary">
        Edit
    </a>
</div>
```
         
### Apply your styling

Now that you have the design elements added to the markup, you are free to express their styling (not shown here for brevity).
