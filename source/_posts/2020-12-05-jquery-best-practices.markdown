---
layout: post
title: "jQuery best practices"
date: 2020-12-05 08:20:34 +0000
comments: true
categories: 
---

jQuery extends the browser's JavaScript interface, often providing thin wrappers for functions you can perform in plain JavaScript with a similar amount of code. 

<!--more-->

When jQuery is wrapping a native feature, it is doing so to provide two benefits:

* It normalizes differences between browser JavaScript interfaces, adapting them into a single jQuery one
* It provides an alternative paradigm for interacting with the DOM (one that is more functional in nature, and less procedural or imperative than the native JavaScript methods)   

This can be thought of as normalizing the differences across browser _types_ and providing a different way of approaching the problems jQuery solves.

When jQuery is providing its own features or implementations, it is doing so to either:

* Polyfill or normalize features or concepts that are may not be implemented natively, particularly in older browsers. 
* To fill in gaps of functionality that do not exist at all in the underlying interface

This can be thought of as normalizing between _versions_ of browsers and providing entirely new functionality, unavailable natively in any of them. 

Having a solid understanding of when jQuery is wrapping a native feature, and when it is providing its own implementation, is the key to understanding how to write performant jQuery.

The following is not intended as a comprehensive guide, but more a useful overview and grouping of the functionality jQuery can provide. Please consult the official jQuery API docs for more information - particularly on what methods may be available for the version of jQuery you're using.

## jQuery objects

jQuery object are usually instantiated with CSS selectors (or similar), that match 0 or more DOM elements.

They're an example of the _Composite_ design pattern, whereby a single object may be interacted with (i.e. provide the same interface) as a collection of objects, and vice versa.

Once instantiated, they provide 3 major pieces of functionality:

* Getters, which allow accessing attribute and property values of _the first_ element matched.
* Setters, which allow setting attribute and property values of _all_ the elements matched.
* Providing a scope to then perform further filtering or DOM traversal

## jQuery object are sets

Although you often interact with jQuery objects as if they are single DOM elements, it's important to think of them as always representing a set, rather than a single value (even if you know that set contains a single element).

### Operate on the set rather than the elements

Perform actions on the set, rather than attempting to iterate over the elements individually:

```javascript 
// Bad
$( "li" ).slice( 2 ).map(function() {
  $(this).css( "background-color", "red" );
});

// Good
$( "li" ).slice( 2 ).css( "background-color", "red" );
```     

### Use set operators instead of logic ones

It's important to understand where applying functions to sets removes the need for explicit logic. For example:

```javascript 
// Bad
var $foo = $('.foo');

if ($foo.length > 0) {
  return $foo.data('bar');
} else {
  return undefined;
}

// Good
$('.foo').data('bar');
```           

### Use the union set operator rather than repeated logic

You can union two sets of elements, (or add an individual element to an existing set) where you want to then work on them as a common set. This often simplifies logic and removes duplication.

```javascript
$union = $( "p" ).add($("div"));
```

### Handle (and use) empty sets

Because the jQuery objects may match no elements (an empty set), you may need to consider when this happens in your code. Empty sets return `undefined` for getters, and perform a no-operation for setters.

Some expressions are simpler by explicitly passing an empty set; You can do so with an empty selector:

```javascript
$()
``` 
   
## Selectors and the DOM 

### Prefer ids

Where possible, use an id selector to find the first element with that id (it's invalid to have a HTML document with multiple elements with the same id). This delegates to native browser functions which are optimised and the fastest way to locate an element.

jQuery objects appear to lazily evaluate their selectors, so unless you call a method on them, they incur no cost.

```javascript
$foo = $('#foo');
$bar = $('#bar');

if (hideFoo) {
  $foo.hide(); // $bar isn't used, so isn't found
} else {
  $bar.hide(); // $foo isn't used, so isn't found
}
```                                

### Prefer to avoid jQuery filter extensions

If using an id to find an element is not possible (or you need to match more than one element), stick to plain CSS selectors, if you can. These are much more efficient than the syntactic sugar jQuery provides. 

In general, check the docs before using any of the following, to understand if there is a more performant CSS-only alternative:

```
:header   :input       :hidden
:first    :button      :visible
:eq()     :password    :animated
:lt()     :checkbox
:gt()     :file
:even     :image
:odd      :radio
:last     :reset   
          :selected
          :submit
          :contains
          :has
``` 

### Use performant CSS selectors

A full discussion of what CSS selectors are the most performant is outside the scope of this article, but some general heuristics are as follows (each comes at the price of less specificity, which may or may not work with your document - consider your use case before applying):

These are heuristics only; the size of their effect (and even if that effect is net positive or negative) will depend on your document, the version of jQuery, web browser and device you're using. Always measure performance yourself and avoid premature optimisation. 

It may be helpful when reading the following to know that CSS selectors are applied right-to-left (i.e. starting with leaf nodes and then walking the DOM back up to the root).

Filtering based on hierarchy is expensive (particularly when it's not an immediate child match). Try to filter only on tag, class and attribute type where possible:

```javascript
// Bad
$('.foo .bar .baz');

// Better
$('.foo > .bar > .baz');

// Best
$('.bar > .baz');             

// Or, if it's possible to change your HTML
$('.bar-baz');             
```                 

Use selectors with as few components as possible (it's less to match):

```javascript
// Bad 
$('input.my-input[type="text"][name="my_input"]');

// Good
$('input[name="my_input"]');
```

Use less permissive operators where possible:

```javascript
// Bad 
$('input[name*="y_in"]');

// Good
$('input[name="my_input"]');
```  

### Perform filtering in CSS where possible

Perform as much filtering as you can in CSS to avoid instantiating more jQuery objects in memory than you need, and performing the equivalent in the relatively slow JavaScript:

```javascript
// Bad 
$('.foo').first();

// Good
$('.foo:first-of-type');
```  

### Search only the part of the document you need 

Where possible, search only the descendants of a known ancestor, rather than the entire document:

```javascript
// Bad
$("span")

// Good
$( "div.foo" ).click(function() {
  $( "span", this ).addClass( "bar" );
});

// Or
$( "div.foo" ).find('span'); 
```

### Reuse references to ancestors 

Often you will already have a ancestor element in scope. Use it to restrict further searching:

```javascript
// Bad
$( "div.foo" ).addClass('bar');
$( "div.foo baz" ).addClass('zap');

// Good
$foo = $( "div.foo" ).addClass('bar');
$foo.find('baz').addClass('zap');
```    

### Prefer selection over traversal

You can think of traversal as jumping to a convenient nearby element and then performing a smaller hop to the element you're actually interested in. Selection, however, is jumping straight to the element you're interested in.

jQuery provides methods for both, and they both have their place (traversal is particularly useful for moving between sibling elements, for example), but always carefully consider selection before resorting to traversal:

```javascript
// Bad
$('.foo').children(function() {
  if ($(this).attr('class') == 'baz') {
     doSomething(this);
  }
});

// Good
$('.foo > .baz').each(function (element) {
  doSomething($(element));
});
``` 

## Getters and Setters

jQuery getters and setters are both called on sets. However, the getter returns the matching attribute of only the _first_ element, while setters set the value of the attributes of _all_ matching elements. To get the attributes of all matched elements, you must use `each()` or `map()`:

```javascript
// Return the name attribute of the first matched element
$('.foo').attr('name'); 

// Return the name attribute of all matched element
$('.foo').map(function() {
  return $(this).attr('name');
});                                                      

// Set the name of all matching elements
$('.foo').attr('name', 'baz');
```
 
 Setter methods are designed to be chained, and return the jQuery object, allowing several method calls to be chained in somewhat of a _Builder_ design pattern. Getters, on the other hand, return the value of the attribute you're requesting.
 
If you want to set an attribute based on logic at call-time (or the element's current value), you can pass a function to setter methods to return the desired new value, which is called once for each HTML element in the matching set. The function is passed the element's index in the set as the first argument, and its current value as the second. It is also bound to the current element, so `this` refers to it.  

jQuery treats HTML elements as having four different attribute types, which you must get and set differently (particularly in later versions of jQuery):

* Properties: Values that affect the dynamic state of a DOM element without changing the serialized HTML attribute. They often record the result of the user interacting with the HTML document, and are oriented around recording some sort of state.
* Value: These are a special type of property, that apply to some elements (particularly form elements) which have values associated with them. 
* Attributes: Can be thought of as everything else, but are more specifically attributes of the element, and are part of the HTML document.
* Data attributes: These are a special type of pseudo-attribute that contain arbitrary custom data associated with the element, bootstrapped from the values stored as part of the HTML document as `data-*` HTML attributes (although they can be added to or modified in jQuery afterwards without being necessary written back to the DOM).   

Examples of properties:

```
tagName
nodeName
nodeType
ownerDocument

selectedIndex
defaultChecked
defaultSelected
checked
selected
disabled
onclick
```

To get and set properties:

```javascript
// Get property of first matched element
$('.foo').prop('bar');

// Set property on all matching elements
$('.foo').prop('bar', 'baz');  

// Bulk property assignment 
$('.foo').prop({ bar: 'baz', bawp: 'woop' });  

// Set using function
$('.foo').prop('bar', function(index, property) {
  return property + ' again'; 
});                                           

// Remove property from all matched elements
$('.foo').removeProp('bar');
``` 

To get and set values:

```javascript
// Get current value of first matching element
$('.foo').val();
                
// Set value on all matching elements
$('.foo').val('bar'); // Must manually trigger change event with $('.foo').trigger("change")

// Bulk set - matching elements a current value that matches one of the elements 
// in the array are checked/selected (those that don't will be unchecked/unselected)               
$('.foo').val(['Yes', 'OK']); 
               
// Set using function
$('.foo').val('bar', function(index, value) {
  return !value; // return undefined if you want to not change value  
});       
```

To get and set attributes:

```javascript
// Get attribute of first matched element
$('.foo').attr('bar');

// Set attribute on all matching elements
$('.foo').attr('bar', 'baz');  

// Bulk attribute assignment 
$('.foo').attr({ bar: 'baz', bawp: 'woop' });  

// Set using function
$('.foo').attr('bar', function(index, attribute) {
  return attribute + ' again'; 
});                                           

// Remove
$('.foo').removeAttr('bar');
```   

To get and set data:

* When getting and setting values as data, it is converted to a string and then back to a JavaScript object, unless that conversion result in rounding errors (floats, for example, are left as strings).

```javascript
// Return all data values associated with first matching element
$('.foo').data();

// Return specific data value associated with first matching element
$('.foo').data('bar');      

// Set data of all matching elements
$('.foo').data('bar', 'baz');

// Remove
$('.foo').removeData('bar');
```      

## Styling

### Working with classes

A common way to apply styling is to manipulate a DOM element's classes (thereby changing the style rules that match the element).

To query whether an element currently has a class:

```javascript
$('.foo').hasClass('bar');
```

Modifying an element's classes:

```javascript
// Add a class to all matching elements
$('.foo').addClass('bar'); 

// Add multiple classes
$('.foo').addClass('bar baz');
$('.foo').addClass(['bar', 'baz']);

// Add class based on current classes
$('.foo').addClass(function(index, currentClasses) {
  return 'newClass';
});                 

// Toggle a class
$('.foo').toggleClass('bar');
$('.foo').toggleClass('bar baz');

// Toggle class based on a a value
$('.foo').toggleClass('bar', shouldAddClass);

$('.foo').toggleClass(function(index, currentValue, isToggled) {
  // Return class to consider toggling
});  

// Replace all classes
$('.foo').attr('class', 'new classes');
                        
// Remove specific class
$('.foo').removeClass('bar');
$('.foo').removeClass('bar baz');
$('.foo').removeClass(['bar', 'baz']);

// Remove all classes
$('.foo').removeClass();     

$('.foo').removeClass(function(index, currentvalue) {
  // Return class to remove
});
```

## Calculated values, dimensions and positions

Sometimes it's not possible to achieve the styling or functionality you need through CSS alone; this is where jQuery's dimensions methods come in handy. They normalize the differences between quantities reported by browsers into a unified interface.

### CSS values

To query the CSS values of elements (property names are camelcased):

```javascript
$('.foo').css('borderTopWidth');

// Bulk query (returns results as an array)
$('.foo').css(['borderTopWidth', 'borderRightWidth']);
```
          
### Dimensions

jQuery's dimension methods return unit-less pixel values, which may be not be whole numbers.

* They return the same values, regardless of the CSS `box-sizing`
* The values are incorrect if the user has zoomed in or out 
* The values may be incorrect if the parent element is hidden (jQuery temporarily shows, measures and then hides the element - which can have a large impact on performance)

```javascript
// Element dimensions only
$('.foo').height();
$('.foo').width();

// Dimensions + padding
$('.foo').innerHeight();
$('.foo').innerWidth();

// inner* + border
$('.foo').outerHeight();
$('.foo').outerWidth();

// outer* + margin
$('.foo').outerHeight(true);
$('.foo').outerWidth(true);

// Get the dimensions of the browser viewport
$(window).height();

// Get the dimensions of the document
$(document).height();
```

### Positions
              
#### Offset

An element's offset is the absolute coordinate of it's _border box_ relative to the `document`  (as an object with `left` and `top` attributes).

* Does not support hidden elements
* Does not include the margin of the `html` tag, if appropriate
* Values are incorrect if the page is zoomed in or out

```javascript
// Get offset of first matching element
$('.foo').offset();

// Set offset of all matching elements
$('.foo').offset({ top: 10, left: 30 });

$('.foo').offset(function(index, currentCoordinates) {
  // return new coordinates
});
```
                   
#### Relative positions

To get the position of an element's _padding box_ to its parent:

```javascript
$('.foo').position();

// Get the closest ancestor that is positioned (relative, absolute or fixed):
$('.foo').offsetParent();
```

#### Scroll position
 
An element's scroll position is the number of pixels hidden from view, above the scrollable area.

* If the element is not scrolled, the value is 0

```javascript
// Get the scroll position of the first matching element
$('.foo').scrollTop();
$('.foo').scrollLeft();

// Set the scroll position of all matching element
$('.foo').scrollTop(10);
$('.foo').scrollLeft(10);
```

## DOM Mutation

JQuery provides the ability to not only filter and locate elements in the DOM, but also to mutate it.  

### Parsing HTML strings into HTML elements

Parse a HTML string into an array of DOM nodes:

* Doesn't remove any leading or trailing whitespace (use `$.trim()` first, for that)

```javascript
$.parse(HTMLString)
```

Use for an iframe:

* Default for before jQuery 3.0: `context` is the current `document` (or if you pass `undefined` or `null`); 
* Default for jQuery 3.0: A new `document` is used. (`scripts` now don't automatically run on the current document and you have the chance to iterate over the HTML and remove anything harmful before it’s added into the current `document`.)

```javascript
$.parse(HTMLString, context)
```

Parse and keep the `script` tags:

* Must explicitly opt in
* Not true of most other methods in jQuery - they’ll keep `script` tags in and likely execute them

```javascript
$.parse(HTMLString, context, true)  
```

### Replacing Elements

#### Outside replace (replace entirely)

Use `replaceAll` when you already have the replacement as a jQuery object, and `replaceWith` when you already have the target to be replaced, as a jQuery object:

* When `replaceWith` is passed a jQuery object that matches a single element, it has the effect of moving that element from its current position in the DOM.

```javascript
$('<div class="foo" />').replaceAll($('.target'));

$('.target').replaceWith('<div class="foo" />');

$('.target').replaceWith(function(index, oldHTML) {
  return '<div class="foo" />'; 
});
```

#### Inside replace (replace contents only)

```javascript
// Replace contents with HTML (prefer text where you can, for security)
$('.foo').html('<p>Foo</p>');                                          

// Replace contents with text (will escape HTML)
$('.foo').text('Foo');
```
        
### Removing elements

#### Removing an element's parent

```javascript
// Remove parents of all matched elements
$('.foo').unwrap();

// Remove parents of all matched elements, if that parent matches a selector
$('.foo').unwrap('.bar');
```   

#### Outside remove (remove entirely)

```javascript    
// Remove all matched elements from the DOM, but keep data and event handlers
// Useful for re-attaching in the future
$detached = $('.foo').detach();                     

// Remove all matched elements, including associated data and event handlers
$('.foo').remove();
```                

#### Inside remove (remove contents)

```javascript
// Remove all contents and children (including associated data and event handlers) from all matched elements
$('.foo').empty();
```               

### Appending elements

#### Outside append (append siblings)

Use `insertAfter` when you already have the HTML to append as a jQuery object, and `after` when you already have the target to append to, as a jQuery object:

* When `after` is passed a jQuery object that matches a single element, it has the effect of moving that element from its current position in the DOM.

```javascript
$('<div class="foo" />').insertAfter($('.target'));

$('.target').after('<div class="foo" />');

$('.target').after(function(index, oldHTML) {
  return '<div class="foo" />'; 
});
```

#### Inside append (append children)

Use `appendTo` when you already have the HTML to append as a jQuery object, and `append` when you already have the target to append to, as a jQuery object:

* When `append` is passed a jQuery object that matches a single element, it has the effect of moving that element from its current position in the DOM.

```javascript
$('<div class="foo" />').appendTo($('.target'));

$('.target').append('<div class="foo" />');

$('.target').append(function(index, oldHTML) {
  return '<div class="foo" />'; 
});
```

### Prepending elements

#### Outside prepend (prepend siblings)

Use `insertBefore` when you already have the HTML to prepend as a jQuery object, and `before` when you already have the target to prepend to, as a jQuery object:

* When `before` is passed a jQuery object that matches a single element, it has the effect of moving that element from its current position in the DOM.

```javascript
$('<div class="foo" />').insertBefore($('.target'));

$('.target').before('<div class="foo" />');

$('.target').before(function(index, oldHTML) {
  return '<div class="foo" />'; 
});
```

#### Inside prepend (prepend children)

Use `prependTo` when you already have the HTML to prepend as a jQuery object, and `prepend` when you already have the target to prepend to, as a jQuery object:

* When `prepend` is passed a jQuery object that matches a single element, it has the effect of moving that element from its current position in the DOM.

```javascript
$('<div class="foo" />').prependTo($('.target'));

$('.target').prepend('<div class="foo" />');

$('.target').prepend(function(index, oldHTML) {
  return '<div class="foo" />'; 
});
```   

### Wrapping elements

#### Outside wrap (Adding parent)

To wrap matching elements in a selector, HTML string, element or jQuery Object (first matching element is used):

```javascript
// Wrap each matching elements in HTML
$('.foo').wrap('<div class="bar"></div>');

$('.foo').wrap(function(index) {
  return "<div class='" + $( this ).text() + "'></div>";
});      

// Wrap all matching elements with the SAME parent
$('li.foo').wrapAll('<ul></ul>');
```  

#### Inside wrap (Adding children to wrap all current children)

```javascript
// Wrap the contents of each matching elements in HTML
$('.foo').wrapInner('<div class="bar"></div>');
```   
   
## Animation

If you need to make animated changes, (which can't be achieved with CSS or manipulating the DOM), jQuery provides a series of functions, each with the same signature:

```javascript
$('.foo').methodName();
$('.foo').methodName(duration);
$('.foo').methodName(duration, callback);
$('.foo').methodName(duration, easing, callback);
$('.foo').methodName(duration, options);
$('.foo').methodName(options);
```

These functions work by animating `width`, `height` or `opacity` (sometimes all 3) over a duration period, and then setting the `display` value to `none` (when hiding), or to restore it its previous value (when showing).

To animate all 3 properties:

```javascript
// Hide and show immediately
$('.foo').hide();
$('.foo').show();
$('.foo').toggle();

// Hide and show over a duration in ms
$('.foo').hide(100);
$('.foo').show(100);
$('.foo').toggle(100);
```

To animate the `opacity` property (causing the elements to appear to fade in and out):

```javascript
// Does not unhide the element; can specify final opacity (Default: 100)
$('.foo').fadeIn(); 

// Unhides a hidden element and fades in or out (always to 100% opacity)
$('.foo').fadeTo();
$('.foo').fadeOut();
$('.foo').fadeToggle();
```

To animate the `height` property (causing the content below to appear to slide up):

```javascript
$('.foo').slideUp();
$('.foo').slideDown();
$('.foo').slideToggle();
```

These methods are actually convenience wrappers around a much more powerful animation function that allows you to animate any numeric style property:

```javascript
$('.foo').animate({
  opacity: 0.25,
}, 5000, callback);
```

The full list of animation options are detailed [here](https://api.jquery.com/animate/).

## Events and user interaction

### The event object

To understand how jQuery handles events, it's important to have a familiarity with the jQuery event object. It's a superset (or extension) of those provided by browsers natively, with differences in behaviour normalized away. The native browser event is always available on the jQuery one as `event.originalEvent`.
                    
It has the following properties (values are populated, depending on the type of event being triggered):

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 284px;"/>
        <col style="width: 531px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td colspan="2"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 815px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Elements</span></div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">target</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>DOM element that initiated the event.</div>
            <ul>
                <li>
                    useful to compare <span
                            style="font-family: &quot;Courier New&quot;;">event.target</span> to this in order
                        to determine if the event is being handled due to event bubbling
                </li>
            </ul>
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">currentTarget</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Current DOM element within the event bubbling phase.</span>
            </div>
            <ul>
                <li>
                    <span
                            style="font-family: &quot;Helvetica Neue&quot;;">Typically equal to </span><span
                            style="font-family: &quot;Courier New&quot;;">this</span><span
                            style="font-family: &quot;Helvetica Neue&quot;;"> in handler function</span>
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">delegateTarget</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Element where the currently-called jQuery event handler was attached</div>
            <ul>
                <li>
                    used, for example, to identify and remove event handlers at the delegation point.
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$( ".box" ).on( "click", "button", function( event ) {</div>
                <div>  $( event<span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.delegateTarget</span>
                    ).css( "background-color", "red" );
                </div>
                <div>});</div>
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="2"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 815px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Propagation</span></div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">isDefaultPrevented()</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Whether <span style="font-family: &quot;Courier New&quot;;">event.preventDefault()</span> was
                ever called on this event object.
            </div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">isImmediatePropagationStopped()</span>
            </div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Whether <span
                    style="font-family: &quot;Courier New&quot;;">event.stopImmediatePropagation()</span> was
                ever called on this event object.
            </div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">isPropagationStopped()</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Whether <span style="font-family: &quot;Courier New&quot;;">event.stopPropagation()</span>
                was ever called on this event object.
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="2"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 815px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Keyboard</span></div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">metaKey</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Whether the META key was pressed when the event fired.</div>
            <ul>
                <li>
                    Mac: Command Key, Windows: Windows key
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">which</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>For key or mouse events, this property indicates the specific key or button that was
                pressed.
            </div>
            <ul>
                <li>
                    normalizes <span style="font-family: &quot;Courier New&quot;;">event.keyCode</span>
                        and <span style="font-family: &quot;Courier New&quot;;">event.charCode</span>.
                </li>
                <li>
                    normalizes button presses (<span style="font-family: &quot;Courier New&quot;;">mousedown</span>
                        and <span style="font-family: &quot;Courier New&quot;;">mouseup </span><span
                                style="font-family: &quot;Helvetica Neue&quot;;">events</span>), reporting 1
                        for left button, 2 for middle, and 3 for right. Use <span
                                style="font-family: &quot;Courier New&quot;;">event.which</span> instead of
                        <span style="font-family: &quot;Courier New&quot;;">event.button</span>.
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td colspan="2"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 815px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Mouse</span></div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">pageX</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>mouse position relative to the left edge of the document.</div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">pageY</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>mouse position relative to the top edge of the document.</div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">relatedTarget</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Other DOM element involved in the event, if any.</div>
            <ul>
                <li>
                    For <span style="font-family: &quot;Courier New&quot;;">mouseout</span>, indicates
                        the element being entered; for <span style="font-family: &quot;Courier New&quot;;">mouseover</span>,
                        indicates the element being exited.
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td colspan="2"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 815px; padding: 8px;">
            <div>General</div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">timeStamp</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Time in milliseconds</div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">type</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Type of event</div>
        </td>
    </tr>
    <tr>
        <td colspan="2"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 815px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Custom &amp; Message pasing</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">data</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Optional object of data passed to an event method when the current executing handler is
                bound.
            </div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">result</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Last value returned by an event handler that was triggered by this event, unless the value
                was undefined.
            </div>
            <ul>
                <li>
                    useful for getting previous return values of custom events.
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$( "button" ).click(function( event ) {</div>
                <div>  return "hey";</div>
                <div>});</div>
                <div>$( "button" ).click(function( event ) {</div>
                <div>  $( "p" ).html( event<span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.result</span> );
                </div>
                <div>});</div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="width: 284px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">namespace</span></div>
        </td>
        <td style="width: 531px; padding: 8px; border: 1px solid;">
            <div>Namespace specified when the event was triggered.</div>
        </td>
    </tr>
    </tbody>
</table>                   

### Browser events

#### Document ready

To attach behaviour as soon as the DOM becomes safe to manipulate:

* Good time to perform tasks needed before the user views or interacts with the page, such as adding event handlers and initializing plugins
* Most browsers provide the equivalent `DOMContentLoaded` event, however if you bind handlers to this event after it's already been triggered, the handlers are never invoked. jQuery's method will call any handlers bound after the event, immediately, so you don't have to worry about your setup code being skipped.

```javascript
$(function() {
    // Do something
});
```

To wait for all assets (including images) on the page to load:

```javascript
$(window).on('load', function() {
    // Do something
});
```

#### Window resizing

```javascript
$( window ).resize(function() {
  $( "#log" ).append( "<div>Handler for .resize() called.</div>" );
});

// Detach
$(window).off( "resize" );

// Manually trigger event
$( window ).resize();
```

#### Window and elements scroll

```javascript
$( "#target" ).scroll(function() {
  $( "#log" ).append( "<div>Handler for .scroll() called.</div>" );
});

// Detach
$(window).off("scroll");

// Manually trigger event
$( "#target" ).scroll();
```

### Binding event handlers

In order to bind events to elements, they must exist in the DOM at the time. This is why you should always wait for the document to be ready before attempting to bind handlers:

```javascript
$(function() {
  $('.foo').on('click', handler);
});
```

You also need to bind you event handlers to elements _above_ any content that may be modified after the initial document is ready (this includes DOM mutations you perform yourself, or content you may load via AJAX), and use event delegation to call the correct handler.

```javascript
// A table that may have its rows modified after the DOM has loaded
$( "#dataTable tbody" ).on( "click", "tr", function() {
  console.log( $( this ).text() );
});
```

In generally, event handlers (particularly delegated ones) should be bound to as small and as few a parts of the document as possible - particularly for events that are triggered many times a second like `scroll` and `mousemove`. You want to avoid listening to events for other parts of the document that you don't end up handling. 

### Event bubbling and event delegation

Browser events bubble, or propagate, from the deepest, innermost element in the document where they occur (the event target), up to the root `document`. jQuery uses this fact (and actually simulates it in some cases) to provide event delegation: whereby you can bind an event handler for an element to its ancestor, by providing a selector to match events that originate from the event target.

Using event delegation makes it easier to bind event handlers to elements that may change after the DOM has loaded (see above); it also makes for more performant code in circumstances where binding an event handler to a single ancestor saves you having to do so for many individual elements: 

```javascript
// Bind to a (single) table body, for a table that may have 1000s of rows
$( "#dataTable tbody" ).on( "click", "tr", function() {
  console.log( $( this ).text() );
});
```

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 184px;"/>
        <col style="width: 107px;"/>
        <col style="width: 107px;"/>
        <col style="width: 192px;"/>
        <col style="width: 224px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="height: 56px; vertical-align: bottom; border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;"
            rowspan="2">
            <div>Binding Type</div>
        </td>
        <td style="height: 56px; vertical-align: bottom; border: 1px solid rgb(204, 204, 204); width: 107px; padding: 8px;"
            rowspan="2">
            <div><br/></div>
            <div>Descendant Selector used</div>
        </td>
        <td style="height: 56px; vertical-align: bottom; border: 1px solid rgb(204, 204, 204); width: 107px; padding: 8px;"
            rowspan="2">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Value of </span><span
                    style="font-family: &quot;Courier New&quot;;">this</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;"> is element</span></div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 416px; padding: 8px;">
            <div style="text-align: center;">Handler is called when event</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 192px; padding: 8px;">
            <div>Directly occurs on target</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 224px; padding: 8px;">
            <div>Occurs on descendants</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Direct</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 107px; padding: 8px;">
            <div>No</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 107px; padding: 8px;">
            <div>Where the handler was attached</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 192px; padding: 8px;">
            <div>Yes</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 224px; padding: 8px;">
            <div>Yes</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 184px; padding: 8px;">
            <div>Delegated</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 107px; padding: 8px;">
            <div>Yes</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 107px; padding: 8px;">
            <div>Matching selector (not where event occurred)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 192px; padding: 8px;">
            <div>No</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 224px; padding: 8px;">
            <div>Yes</div>
        </td>
    </tr>
    </tbody>
</table>

#### Preventing propagation and default behaviour

Sometimes it's preferable to prevent event propagation (so an ancestor element doesn't try to handle an event a descendant has already handled, for example). This is prevented with `.stopPropagation()`.

Some elements also have a default behaviour the browser invokes when an event occurs (e.g. a link is followed when clicked on). This can be prevented by calling `.preventDefault()` on the event object.

Preventing the default behaviour and stopping propagation will not prevent any other jQuery handlers bound to that element from running. Use `.spotImmediatePropagation()` to prevent those from running.

```javascript
// Returning false from a handler is equivalent to preventing the default and stopping propagation
$( "#foo" ).on( "click", function() {
  // ...
  return false;
});

// Shorthand for function(){ return false; }
$( "#foo" ).on( "click", false);

// Cancel only the default action
$( "form" ).on( "submit", function( event ) {
  event.preventDefault();
});

// Only prevent an action from bubbling up
$( "form" ).on( "submit", function( event ) {
  event.stopPropagation();
});

// Prevent any other handlers that may have been bound to this event and element
$( "form" ).on( "submit", function( event ) {
  event.spotImmediatePropagation();
});
```

### Events that don't bubble

`focus` and `blur` events are specified by the W3C to not bubble. However, jQuery defines custom cross-browser `focusin` and `focusout` events that do bubble. When `focus` and `blur` are used to attach _delegated_ event handlers, jQuery maps them internally to `focusin` and `focusout`.

`load`, `scroll`, and `error` events also do not bubble.

In IE 8 and lower, the `paste` and `reset` events do not bubble.

### Manually triggering events

You can manually simulate the triggering of events, complete with event bubbling.

* For plain objects and DOM objects other than `window`, if a triggered event name matches the name of a property on the object, jQuery will attempt to invoke the property as a method (if no event handler calls `event.preventDefault()`).
* If an event name matches the name of a property on the object, prefixed by `on`, jQuery will attempt to invoke that property as a method.

```javascript
// Trigger event for all matching elements
$('.foo').trigger('click');
```

If you don't want to trigger any native property with a mtching name, or have event bubbling: 

* Triggers any handlers attached through jQuery and any native methods attached to the element with the same name, prefixed by `on`.
* Events do not bubble up the DOM hierarchy
* Returns result of the handler, rather than the jQuery object for further chaining
* Won't call `eventName()` on the element (e.g `triggerHandler('submit')` won't call `.submit()`)
                                  
```javascript                                                                      
// Trigger jQuery handlers (only) for the *first* matched element
$('.foo').triggerHandler('click');
```

### Removing event handlers

Automatically unbind a handler after its first invocation:
                                                          
* Takes same arguments as `on()`

```javascript
$('.foo').one('click', function() {
  // Do something the first time an event occurs
});
```

Removing handlers:

```javascript
// Remove all handlers (for any event) attached to all matching elements
$('.foo').off();

// Remove handlers only for a particular event
$('.foo').off('click');

// Remove all handlers within a custom namespace
$('.foo').off('click.myPlugin');

// Remove delegated handlers
$('.foo').off('click', 'tr');

// Remove all delegated handlers, leaving non-delegated ones
$('.foo').off('click', '**');

// Remove specific handler function
$('.foo').off('click', myFunction);
```

### Custom events

If a handler is bound to a string value that is not the name of a native DOM event, then it is considered a custom event. These are never called by the browser, but may be triggered manually.

Namespaced events allow triggering or unbinding events without affecting the native ones (or other custom ones). If a event type string contains a period, it's a namespaced event (they should contain only letters and digits).

```javascript
// Event type: click; namespaces: myPlugin, simple
$('.foo').on('click.myPlugin.simple', handler ) 

// Can be removed with either (like CSS classes, namespaced events are not hierarchical; 
// only one of the namespaces needs to match.) 
$('.foo').off('click.myPlugin');
$('.foo').off('click.simple');
```

### Passing custom data to event handlers
                      
Passing data at bind-time:

```javascript
$('.foo').on('click', {
  bar: 'baz'
}, function (event) {
  // Access as event.data.bar;
})
```

Passing at trigger-time:

```javascript
$('.foo').on('click', function(event, arg1, arg2) {
  // Access arg1 and arg3, etc (Note the array elements are split out into separate arguments)
});

$('.foo').trigger('on', ['bar', 'baz']);
```

### Event shorthands
                 
jQuery provides a number of shorthands for common events. They're equivalent to calling the longer `.on(eventName, ...)` form, and accept the same arguments (aside from the event name being defined for you).

```javascript
$('.foo').mousedown(handler) // equivelent of $('.foo').on('mousedown', handler)
```

#### Mouse events

```javascript
// When a mouse pointer is over an element, and the button is pressed
$('.foo').mousedown(handler);

// When a mouse pointer is over an element, and the button is released
$('.foo').mouseup(handler);

// When a mouse pointer is over an element, and the button is both pressed and released without exiting it
$('.foo').click(handler);

// When a mouse pointer is over an element, and the button is both pressed and released 
// (twice) without exiting it (it's not recommended to bind to both click and doubclick; 
// browsers differ in the order they report these events)
// Double-click sensitivity depends on OS, browser and user configuration 
$('.foo').dbclick(handler);

// When a mouse pointer is over an element, and the right button is both pressed and 
// released without exiting it. Triggered before the context menu is displayed.
$('.foo').contextmenu(handler);

// When mouse pointer enters an element (propietary to IE, but jQuery makes it available
// in other browsers)
// Only triggers when pointer enters element it's bound to - not its descendants
$('.foo').mouseenter(handler);

// Like mouseenter, but also gets triggered when mouse enters descendants of the 
// element and bubbles up. i.e. Event can be triggered when re-entering a child 
// without having re-entered the target element
$('.foo').mouseover(handler);

// When mouse moves within an element
// jQuery normalizes the pageX and pageY properties on the event object between browsers
$('.foo').mousemove(handler);

// Complement of mouseenter; when the mouse leaves an element (notes on mouseleave apply 
// here)
$('.foo').mouseleave(handler);

// Complement of mouseover; when the mouse leaves an element (notes on mousover apply 
// here)
$('.foo').mouseout(handler);

// When a mouse enters or leaves an element
$('.foo').hover(handler);
$( selector ).hover( handlerIn, handlerOut );
```

Mouse event objects have the following values for `which`:

* `1` For left button
* `2` For right button
* `3` For middle button

#### Keyboard events

Keyboard events are sent to the element that is currently in focus (focusable elements vary between browsers).

Global keyboard events can be captured by attaching to `document`.

In order to determine which key was the subject of an event, jQuery normalizes this information into the `which` event property, which contains a key code. For keydown and keyup events, its value does not change for uppercase letters. For the keypress event, it's value is different for uppercase letters. E.g. a lowercase "a" will be reported as `65` by keydown and keyup, but as `97` by keypress. An uppercase "A" is reported as `65` by all events.

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 131px;"/>
        <col style="width: 134px;"/>
        <col style="width: 78px;"/>
        <col style="width: 78px;"/>
        <col style="width: 140px;"/>
        <col style="width: 163px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 134px; padding: 8px;">
            <div>When</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 156px; padding: 8px;" colspan="2">
            <div>Triggered by modifier &amp; non-printing keys</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 140px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">.which</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;"> code indicates</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 163px; padding: 8px;">
            <div>Covered by specification</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div>Keydown</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 134px; padding: 8px;">
            <div>User presses key (every time OS repeats key)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 156px; padding: 8px;" colspan="2">
            <div>Yes</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 140px; padding: 8px;">
            <div>key pressed</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 163px; padding: 8px;">
            <div>Yes</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div>Keypress</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 134px; padding: 8px;">
            <div>Browser registers keyboard input</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 156px; padding: 8px;" colspan="2">
            <div>No</div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 140px; padding: 8px;">
            <div>Character entered</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 163px; padding: 8px;">
            <div>No</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 131px; padding: 8px;">
            <div>Keyup</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 134px; padding: 8px;">
            <div>User releases key (every time OS repeats key)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 156px; padding: 8px;" colspan="2">
            <div>Yes</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 140px; padding: 8px;">
            <div>key pressed</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 163px; padding: 8px;">
            <div>Yes</div>
        </td>
    </tr>
    </tbody>
</table>

```javascript
$('.foo').keydown() // shortcut for .on('keydown', ...);
$('.foo').keypress() // shortcut for .on('keypress', ...);
$('.foo').keyup() // shortcut for .on('keyup', ...);
```

#### Focus events
        
Historically browsers limited focusable elements to some form elements, but modern browsers extend this to other elements that have a `tabindex` property set.

```javascript
// When any of the matching elements are focused
$('.foo').focus() // shorthand for .on('focus', ...)

// Include when any of an elements descendants are focused 
$('.foo').focusin() // shorthand for .on('focusin', ...)

// When any of the matching elements lose focus
$('.foo').blur() // shorthand for .on('blur', ...)

// Include when any of an elements descendants lose focus 
$('.foo').focusout() // shorthand for .on('focusout', ...)
```

#### Change events

The change events are limited to `input`, `textarea` and `select` elements. The event is fired immediately when the user makes a selection for select boxes, checkboxes and radio buttons; the event is deffered for other element types until it loses focus.

Changing the value using JavaScript (e.g. using `.val()`) doesn't fire the event.

```javascript
$('.foo').change() // shorthand for .on('change', ...)
```
   
#### Selection events

Selection can occur for `input` of type `text` and `textarea` elements, when the user selects some of the element's input. The method for retrieving the selected text varies between browsers, but there are jQuery plugins that help normalize it.

```javascript
$('.foo').select() // shorthand for .on('select', ...)
```

#### Form submission events

Submit events only occur on `form` elements and occur when clicking a `input` or `button` of type `submit` or `image`, or by pressing Enter when certain form elements are focused (although this varies between browsers). The event occurs _before_ the actual submission, so you have a chance to prevent it. 

```javascript
$('.foo').submit() // shorthand for .on('submit', ...)
```

## AJAX

### Serializing data for requests

```javascript
// Create URL query string from object (recursive as of jQuery 1.4)
// Not suggested for complex or deeply nested objects
$.param(obj);

// With deep serialization disabled
$.param(obj, true);
```

It's possible serialize the values from a form as a JSON string. Although jQuery can act on selected individual form controls, it's easier so select the `form` tag itself. jQuery will then use the W3C rules for successful controls to determine which elements to include. Namely:

* Submit button values are not serialized when the form was not submitted using a button
* Form elements must have a `name` attribute to be included
* Checkbox and radio button values are included only if they are checked
* Form field data is not serialized

```javascript
// Serialize into a JSON string
$('form').serializeArray();     // Preferred
$(':input').serializeArray();   // Alternative if you need or want to select inputs directly 
```
             
Similarly, for serializing form data as URL-encode strings:

```javascript
// Serialize into URL-encoded string:
$('form').on('submit', function( event ) {
  event.preventDefault();
  console.log($( this ).serialize());
});
```

### jqXHR and Promise objects

All AJAX methods return `jqXHR` objects, which implement the Promise interface:

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 433px;"/>
        <col style="width: 130px;"/>
        <col style="width: 221px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 433px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div>(Deprecated) name (removed in jQuery 3.0)</div>
        </td>
        <td style="width: 221px; padding: 8px; border: 1px solid;">
            <div>Equivalent to callback</div>
        </td>
    </tr>
    <tr>
        <td style="width: 433px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">done(function( data, textStatus, jqXHR ) {});</span>
            </div>
        </td>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">success</span></div>
        </td>
        <td style="width: 221px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">success</span></div>
        </td>
    </tr>
    <tr>
        <td style="width: 433px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">fail(function( jqXHR, textStatus, errorThrown ) {});</span>
            </div>
        </td>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">error</span></div>
        </td>
        <td style="width: 221px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">error</span></div>
        </td>
    </tr>
    <tr>
        <td style="width: 433px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">always(function( data|jqXHR, textStatus, jqXHR|errorThrown ) { })</span>
            </div>
        </td>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">complete</span></div>
        </td>
        <td style="width: 221px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">complete</span></div>
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="width: 433px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">then(function( data, textStatus, jqXHR ) {}, function( jqXHR, textStatus, errorThrown ) {});</span>
            </div>
        </td>
        <td style="width: 130px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 221px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">success</span> and <span
                    style="font-family: &quot;Courier New&quot;;">error</span></div>
        </td>
    </tr>
    </tbody>
</table>

jqXHR objects can be used for setting handlers at the the time the object is instantiated, or at a later point.
  
```javascript
// Assign handlers immediately after making the request, and remember the jqxhr object for this request
var jqxhr = $.get( "example.php", function() {
  alert( "success" );
})
  .done(function() {
    alert( "second success" );
  })
  .fail(function() {
    alert( "error" );
  })
  .always(function() {
    alert( "finished" );
  });

// Perform other work here ...

// Set another completion function for the request above
jqxhr.always(function() {
  alert( "second finished" );
});
```

### AJAX shorthands

jQuery provides a number of convenience methods that wrap the more generalized `ajax` method:

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 408px;"/>
        <col style="width: 408px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 408px; padding: 8px; border: 1px solid;">
            <div>Method</div>
        </td>
        <td style="width: 408px; padding: 8px; border: 1px solid;">
            <div>Equivalent</div>
        </td>
    </tr>
    <tr>
        <td style="width: 408px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$<span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.get(</span> url,
                    data, success, dataType<span
                            style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span></div>
            </div>
        </td>
        <td style="width: 408px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$.ajax({</div>
                <div>  url: url,</div>
                <div>  data: data,</div>
                <div>  success: success,</div>
                <div>  dataType: dataType</div>
                <div>});</div>
                <div><br/></div>
                <div>$.get( "ajax/test.html", function( data ) {</div>
                <div>  $( ".result" ).html( data );</div>
                <div>  alert( "Load was performed." );</div>
                <div>});</div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="width: 408px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$<span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.getJSON(</span>url,
                    data, success<span
                            style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span>;
                </div>
            </div>
        </td>
        <td style="width: 408px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$.ajax({</div>
                <div>  dataType: "json",</div>
                <div>  url: url,</div>
                <div>  data: data,</div>
                <div>  success: success</div>
                <div>});</div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="width: 408px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$<span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.getScript(</span>url,
                    success<span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span>;
                </div>
            </div>
        </td>
        <td style="width: 408px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$.ajax({</div>
                <div>  url: url,</div>
                <div>  dataType: "script",</div>
                <div>  success: success</div>
                <div>});</div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="width: 408px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$<span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.post( </span>url,
                    data, success, dataType<span
                            style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span></div>
            </div>
        </td>
        <td style="width: 408px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$.ajax({</div>
                <div>  type: "POST",</div>
                <div>  url: url,</div>
                <div>  data: data,</div>
                <div>  success: success,</div>
                <div>  dataType: dataType</div>
                <div>});</div>
            </div>
        </td>
    </tr>
    </tbody>
</table>

#### Loading HTML into the DOM

jQuery also provides a helper for loading HTML documents over the AJAX and mounting their response into the DOM.
                  
* Ajax request will not be sent if no element is matched by the selector

```javascript
// Load content and mount its entire response into the DOM
//
// WARNING: the content is passed to .html() PRIOR to scripts being removed. This EXECUTES the script 
// blocks before they are discarded.
$( "#result" ).load( "ajax/test.html" );

// Only mount a subset of the response into the DOM (using a selector to determine which subset)
// This form strips out script tags before the DOM is updated (so they're not executed)
$( "#result" ).load( "ajax/test.html #container" );
```

### AJAX options

This table explains the complete set of AJAX options used with the generalised `$.ajax()` function:

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 130px;"/>
        <col style="width: 128px;"/>
        <col style="width: 125px;"/>
        <col style="width: 125px;"/>
        <col style="width: 299px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div>Settings</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div>Default</div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Description</div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 807px; padding: 8px;">
            <div><span style="color: rgb(0, 0, 0); --inversion-type-color:  simple;">Request Headers</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">accepts</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div>Depends on <span style="font-family: &quot;Courier New&quot;;">dataType</span></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Sets the <span style="font-family: &quot;Courier New&quot;;">Accept</span> header</div>
            <ul>
                <li>
                    Key/value pairs that map <span
                            style="font-family: &quot;Courier New&quot;;">dataType</span> to MIME type
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">contentType</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">application/x-www-form-urlencoded; charset=UTF-8</span>
            </div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>As of jQuery 1.6 you can pass false to tell jQuery to not set any content type header</div>
            <div><br/></div>
            <div>For cross-domain requests, setting the content type to anything other than <span
                    style="font-family: &quot;Courier New&quot;;">application/x-www-form-urlencoded</span>,
                <span style="font-family: &quot;Courier New&quot;;">multipart/form-data</span>, or <span
                        style="font-family: &quot;Courier New&quot;;">text/plain </span>will trigger the
                browser to send a preflight <span style="font-family: &quot;Courier New&quot;;">OPTIONS</span>
                request to the server
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">mimeType</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>mime type to override the XHR mime type</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">headers</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">X-Requested-With: XMLHttpRequest</span>
                is always added, but default value can be changed
            </div>
            <div><br/></div>
            <div>Values can also then be overridden in <span style="font-family: &quot;Courier New&quot;;">beforeSend</span>
                function
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 807px; padding: 8px;">
            <div><span style="color: rgb(0, 0, 0); --inversion-type-color:  simple;">Request Behaviour</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">method</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;"> and </span><span
                    style="font-family: &quot;Courier New&quot;;">type</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">GET</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;" colspan="3">
            <div>HTTP method to use for the request</div>
            <ul>
                <li>
                    Was called <span style="font-family: &quot;Courier New&quot;;">type</span><span
                            style="font-family: &quot;Helvetica Neue&quot;;"> before jQuery 1.9.0</span> 
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">url</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div>Current page</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;" colspan="3">
            <div>URL to send request to</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">async</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">true</span></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;"> If you need synchronous requests, set this option to </span><span
                    style="font-family: &quot;Courier New&quot;;">false</span></div>
            <ul>
                <li>
                    Synchronous requests may temporarily lock the browser, disabling any actions while
                        the request is active
                </li>
                <li>
                    <span style="font-family: &quot;Helvetica Neue&quot;;">Cross-domain requests and dataType: </span><span
                            style="font-family: &quot;Courier New&quot;;">"jsonp" </span><span
                            style="font-family: &quot;Helvetica Neue&quot;;">requests do not support synchronous operation.</span>
                </li>
                <li>
                    As of jQuery 1.8, the use of <span style="font-family: &quot;Courier New&quot;;">async: false</span>
                        with <span style="font-family: &quot;Courier New&quot;;">jqXHR ($.Deferred)</span> is
                        deprecated; you must use the <span style="font-family: &quot;Courier New&quot;;">success</span>/<span
                                style="font-family: &quot;Courier New&quot;;">error</span>/<span
                                style="font-family: &quot;Courier New&quot;;">complete</span> callback options
                        instead of the corresponding methods of the jqXHR object such as <span
                                style="font-family: &quot;Courier New&quot;;">jqXHR.done()</span>.
                </li>
            </ul>
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">cache</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">true</span></div>
            <div><br/></div>
            <div><span
                    style="font-family: &quot;Courier New&quot;;">false for dataType 'script' and 'jsonp'</span>
            </div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">By default, requests are always issued, but the browser may serve results out of its cache</span>
            </div>
            <div><br/></div>
            <div>Set to <span style="font-family: &quot;Courier New&quot;;">false</span> to force requested
                pages not to be cached by the browser
            </div>
            <ul>
                <li>
                    will only work correctly with HEAD and GET requests
                </li>
                <li>
                    works by appending "<span
                            style="font-family: &quot;Courier New&quot;;">_={timestamp}</span>" to the GET
                        parameters
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">crossDomain</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">false</span> for same-domain requests,
                <span style="font-family: &quot;Courier New&quot;;">true</span> for cross-domain requests
            </div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>If you wish to force a crossDomain request (such as JSONP) on the same domain</div>
            <ul>
                <li>
                    Allows, for example, server-side redirection to another domain
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">isLocal</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div>Depends on protocol</div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Allow the current environment to be recognized as "local," (e.g. the filesystem)</div>
            <ul>
                <li>
                    protocols automaticaly recognized as local: <span
                            style="font-family: &quot;Courier New&quot;;">file</span>, <span
                            style="font-family: &quot;Courier New&quot;;">*-extension</span>, and <span
                            style="font-family: &quot;Courier New&quot;;">widget</span>.
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">xhr</span></div>
        </td>
        <td colspan="4" style="border: 1px solid rgb(204, 204, 204); width: 677px; padding: 8px;">
            <div>Callback for creating the <span
                    style="font-family: &quot;Courier New&quot;;">XMLHttpRequest</span> object.
            </div>
            <ul>
                <li>
                     Defaults to the <span
                            style="font-family: &quot;Courier New&quot;;">ActiveXObject</span> when available
                        (IE), the <span style="font-family: &quot;Courier New&quot;;">XMLHttpRequest</span>
                        otherwise.
                </li>
                <li>
                    Override to provide your own implementation for <span
                            style="font-family: &quot;Courier New&quot;;">XMLHttpRequest</span> or
                        enhancements to the factory.
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">xhrFields</span></div>
        </td>
        <td colspan="4" style="border: 1px solid rgb(204, 204, 204); width: 677px; padding: 8px;">
            <div>Object of fieldName-fieldValue pairs to set on the native XHR object.</div>
            <ul>
                <li>
                    Bug: In jQuery 1.5, the <span style="font-family: &quot;Courier New&quot;;">withCredentials</span>
                        property was not propagated to the native <span
                                style="font-family: &quot;Courier New&quot;;">XHR</span> and thus CORS
                        requests requiring it would ignore this flag. 
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$.ajax({</div>
                <div>  url: a_cross_domain_url,</div>
                <div>  <span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">xhrFields: {</span>
                </div>
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">    withCredentials: true</span>
                </div>
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">  }</span></div>
                <div>});</div>
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 807px; padding: 8px;">
            <div><span style="color: rgb(0, 0, 0); --inversion-type-color:  simple;">Authentication</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">username</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Username to be used with <span
                    style="font-family: &quot;Courier New&quot;;">XMLHttpRequest</span> in response to an HTTP
                access authentication request.
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">password</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Password to be used with <span
                    style="font-family: &quot;Courier New&quot;;">XMLHttpRequest</span> in response to an HTTP
                access authentication request.
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 807px; padding: 8px;">
            <div><span style="color: rgb(0, 0, 0); --inversion-type-color:  simple;">Script requests</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">script</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Object with additional attributes to be used in a "script" or "jsonp" request.</div>
            <ul>
                <li>
                    If this object is provided it will force the use of a script-tag transport
                </li>
                <li>
                    Can be used to set <span style="font-family: &quot;Courier New&quot;;">nonce</span>,
                        <span style="font-family: &quot;Courier New&quot;;">integrity</span>, or <span
                                style="font-family: &quot;Courier New&quot;;">crossorigin</span> attributes to
                        satisfy Content Security Policy requirements.
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">scriptCharset</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Sets the charset attribute on the script tag used in the request</div>
            <ul>
                <li>
                    Used when the character set on the local page is not the same as the one on the
                        remote script
                </li>
                <li>
                    <span style="font-family: &quot;Courier New&quot;;">charset</span> attribute can be
                        specified in <span style="font-family: &quot;Courier New&quot;;">scriptAttrs</span>
                        instead
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 807px; padding: 8px;">
            <div>Request content (body or query parameters)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">data</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Data to be sent to the server</div>
            <ul>
                <li>
                    If the HTTP method is one that cannot have an entity body, such as <span
                            style="font-family: &quot;Courier New&quot;;">GET</span>, the data is appended to
                        the URL.
                </li>
            </ul>
            <div><br/></div>
            <div>POST data will always be transmitted to the server using UTF-8 charset, per the W3C
                XMLHTTPRequest standard.
            </div>
            <ul>
                <li>
                    Objects: jQuery generates the data string from the object's key/value pairs using
                        <span style="font-family: &quot;Courier New&quot;;">jQuery.param()</span> unless the
                        <span style="font-family: &quot;Courier New&quot;;">processData</span> option is set
                        to <span style="font-family: &quot;Courier New&quot;;">false</span>
                </li>
                <li>
                    <span style="font-family: &quot;Helvetica Neue&quot;;">Arrays: </span>serializes
                        multiple values with same key based on the value of the traditional setting
                </li>
                <li>
                    String: should already be encoded using the correct encoding for <span
                            style="font-family: &quot;Courier New&quot;;">contentType</span>
                </li>
            </ul>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">In requests with </span><span
                    style="font-family: &quot;Courier New&quot;;">dataType: "json"</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;"> or </span><span
                    style="font-family: &quot;Courier New&quot;;">dataType: "jsonp"</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">, if the string contains a double question mark (??) anywhere in the URL or a single question mark (?) in the query string, it is replaced with a value generated by jQuery that is unique for each copy of the library on the page (e.g. </span><span
                    style="font-family: &quot;Courier New&quot;;">jQuery21406515378922229067_1479880736745</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">).</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">processData</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">true</span></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>By default, data passed in to the <span
                    style="font-family: &quot;Courier New&quot;;">data</span> option as anything other than a
                string will be processed and transformed into a query string, fitting to the default
                content-type <span style="font-family: &quot;Courier New&quot;;">"application/x-www-form-urlencoded”</span>
            </div>
            <ul>
                <li>
                    <span style="font-family: &quot;Helvetica Neue&quot;;">Set to </span><span
                            style="font-family: &quot;Courier New&quot;;">false</span><span
                            style="font-family: &quot;Helvetica Neue&quot;;"> if you want to send a DOMDocument, or other non-processed data</span>
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">traditional</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">true </span>if you wish to use the
                traditional style of param serialization (see serializaiton section above)
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 807px; padding: 8px;">
            <div>Response behaviour</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">contents</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Object of string or regular expression keys that determine how to parse response</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">converters</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>dataType-to-dataType converters:</div>
            <div><br/></div>
            <div>Default:</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>{</div>
                <div>  "* text": window.String, </div>
                <div>  "text html": true, </div>
                <div>  "text json": jQuery.parseJSON, </div>
                <div>  "text xml": jQuery.parseXML</div>
                <div>}</div>
            </div>
            <div><br/></div>
            <div>Use <a href="https://api.jquery.com/jQuery.ajaxTransport/">transports</a> when converters and
                prefilters aren’t flexible enough.
            </div>
        </td>
    </tr>
    <tr>
        <td rowspan="9"
            style="height: 308px; border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">dataType</span></div>
        </td>
        <td rowspan="9"
            style="height: 308px; border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div>Intelligent Guess (<span
                    style="font-family: &quot;Courier New&quot;;">xml, json, script</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">, or </span><span
                    style="font-family: &quot;Courier New&quot;;">htm</span>l)
            </div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Type of data that you're expecting back from the server</div>
            <ul>
                <li>
                     If none is specified, jQuery will try to infer it based on the MIME type of the
                        response
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div>Data type</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div>Preprocessing</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 299px; padding: 8px;">
            <div>Desscription</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">“text"</span></div>
        </td>
        <td style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;"
            rowspan="2">
            <div>None</div>
            <div><br/></div>
            <div>Passed to <span style="font-family: &quot;Courier New&quot;;">success</span> as <span
                    style="font-family: &quot;Courier New&quot;;">jqXHR .</span><span
                    style="font-family: &quot;Courier New&quot;;">responseText </span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">(</span><span
                    style="font-family: &quot;Courier New&quot;;">String</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">)</span></div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 299px; padding: 8px;">
            <div>A plain text string.</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">"html"</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 299px; padding: 8px;">
            <div>Returns HTML as plain text; included <span style="font-family: &quot;Courier New&quot;;">script</span>
                tags are evaluated when inserted in the DOM.
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">"json"</span></div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">jQuery.parseJSON</span></div>
            <div><br/></div>
            <div>Passed to <span style="font-family: &quot;Courier New&quot;;">success</span> as <span
                    style="font-family: &quot;Courier New&quot;;">jqXHR .</span><span
                    style="font-family: &quot;Courier New&quot;;">responseJSON </span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">(</span><span
                    style="font-family: &quot;Courier New&quot;;">Object</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">)</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 299px; padding: 8px;">
            <div>Evaluates the response as JSON and returns a JavaScript object.</div>
            <ul>
                <li>
                    Cross-domain "json" requests that have a callback placeholder, e.g. <span
                            style="font-family: &quot;Courier New&quot;;">?callback=?</span>, are performed
                        using JSONP unless the request includes <span
                                style="font-family: &quot;Courier New&quot;;">jsonp: false</span> in its
                        request options.
                </li>
                <li>
                    The JSON data is parsed in a strict manner; any malformed JSON is rejected and a
                        parse error is thrown.
                </li>
                <li>
                    As of jQuery 1.9, an empty response is also rejected; the server should return a
                        response of <span style="font-family: &quot;Courier New&quot;;">null</span> or <span
                                style="font-family: &quot;Courier New&quot;;">{}</span> instead. (See <a
                                href="http://json.org/">json.org</a> for more information on proper JSON
                        formatting.)
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">"script"</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div>Execute the JavaScript that is received from the server</div>
            <div><br/></div>
            <div>Passed to <span style="font-family: &quot;Courier New&quot;;">success</span> as<span
                    style="font-family: &quot;Courier New&quot;;"> </span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">(</span><span
                    style="font-family: &quot;Courier New&quot;;">S</span><span
                    style="font-family: &quot;Courier New&quot;;">tring</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">)</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 299px; padding: 8px;">
            <div>Evaluates the response as JavaScript and returns it as plain text. Disables caching by
                appending a query string parameter,<span style="font-family: &quot;Courier New&quot;;"> _=[TIMESTAMP]</span>,
                to the URL unless the <span style="font-family: &quot;Courier New&quot;;">cache</span> option
                is set to <span style="font-family: &quot;Courier New&quot;;">true</span>.
            </div>
            <ul>
                <li>
                    Note: This will turn POSTs into GETs for remote-domain requests. Prior to jQuery
                        3.5.0, unsuccessful HTTP responses with a script Content-Type were still executed.
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">"jsonp</span>"</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div>Automatically append a query string parameter of (by default) <span
                    style="font-family: &quot;Courier New&quot;;">callback=?</span> to the URL
            </div>
            <div><br/></div>
            <div>Server should return valid JavaScript that passes the JSON response into the callback
                function.
            </div>
            <div><br/></div>
            <div>jQuery will execute the returned JavaScript, calling the JSONP callback function</div>
            <div><br/></div>
            <div>Passes JSON object containing response to <span
                    style="font-family: &quot;Courier New&quot;;">success</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 299px; padding: 8px;">
            <div>Loads in a JSON block using JSONP. Adds an extra <span
                    style="font-family: &quot;Courier New&quot;;">"?callback=?" </span>to the end of your URL
                to specify the callback.
            </div>
            <ul>
                <li>
                    Disables caching by appending a query string parameter, <span
                            style="font-family: &quot;Courier New&quot;;">"_=[TIMESTAMP]"</span>, to the URL
                        unless the <span style="font-family: &quot;Courier New&quot;;">cache</span> option is
                        set to <span style="font-family: &quot;Courier New&quot;;">true</span>.
                </li>
            </ul>
            <div><br/></div>
            <div><a href="https://bob.pythonmac.org/archives/2005/12/05/remote-json-jsonp/">More info</a>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">"xml"</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;"> jQuery.parseXML</span></div>
            <div><br/></div>
            <div>Passed to <span style="font-family: &quot;Courier New&quot;;">success</span> as <span
                    style="font-family: &quot;Courier New&quot;;">jqXHR .</span><span
                    style="font-family: &quot;Courier New&quot;;">responseXML </span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">(</span><span
                    style="font-family: &quot;Courier New&quot;;">XMLDocument</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">)</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 299px; padding: 8px;">
            <div>Returns XML document that can be processed via jQuery.</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div>multiple, space-separated values</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 125px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 299px; padding: 8px;">
            <div>As of jQuery 1.5, jQuery can convert a <span style="font-family: &quot;Courier New&quot;;">dataType</span>
                from what it received in the <span
                        style="font-family: &quot;Courier New&quot;;">Content-Type</span> header to what you
                require.
            </div>
            <ul>
                <li>
                    For example, if you want a text response to be treated as XML, use <span
                            style="font-family: &quot;Courier New&quot;;">"text xml"</span> for the <span
                            style="font-family: &quot;Courier New&quot;;">dataType</span>. You can also make a
                        JSONP request, have it received as text, and interpreted by jQuery as XML: "jsonp text
                        xml". Similarly, a shorthand string such as "jsonp xml" will first attempt to convert
                        from jsonp to xml, and, failing that, convert from jsonp to text, and then from text
                        to xml.
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">statusCode</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Object of numeric HTTP codes and functions to be called when the response has the
                corresponding code
            </div>
            <ul>
                <li>
                    If the request is successful, the status code functions take the same parameters as
                        the success callback
                </li>
                <li>
                    if it results in an error (including 3xx redirect), they take the same parameters as
                        the error callback.
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$.ajax({</div>
                <div>  statusCode: {</div>
                <div>    404: function() {</div>
                <div>      alert( "page not found" );</div>
                <div>    }</div>
                <div>  }</div>
                <div>});</div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">dataFilter</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Function to be used to handle the raw response data</div>
            <ul>
                <li>
                    pre-filtering function to sanitize the response.
                </li>
                <li>
                    Arguments:
                </li>
                <ul>
                    <li>
                        The raw data returned from the server and
                    </li>
                    <li>
                        The 'dataType' parameter.
                    </li>
                </ul>
                <li>
                    should return the sanitized data
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>Function( String data, String type ) =&gt; Anything</div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">timeout</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><br/></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Timeout (in milliseconds) for the request</div>
            <ul>
                <li>
                    <span style="font-family: &quot;Courier New&quot;;">0</span> means there will be no
                        timeout
                </li>
                <li>
                    Overrides any global timeout set with <span
                            style="font-family: &quot;Courier New&quot;;">$.ajaxSetup()</span>
                </li>
                <li>
                    Starts at the point the <span
                            style="font-family: &quot;Courier New&quot;;">$.ajax</span> call is made (if
                        several other requests are in progress and the browser has no connections available,
                        it is possible for a request to time out before it can be sent)
                </li>
                <li>
                    n jQuery 1.4.x and below, the XMLHttpRequest object will be in an invalid state if
                        the request times out; accessing any object members may throw an exception.
                </li>
                <li>
                    In Firefox 3.0+ only, script and JSONP requests cannot be cancelled by a timeout; the
                        script will run even if it arrives after the timeout period.
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">ifModified</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 128px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">false</span></div>
        </td>
        <td colspan="3" style="border: 1px solid rgb(204, 204, 204); width: 549px; padding: 8px;">
            <div>Successful only if the response has changed since the last request</div>
            <ul>
                <li>
                    Checks <span style="font-family: &quot;Courier New&quot;;">Last-Modified</span>
                        header
                </li>
                <li>
                    As of jQuery 1.4, also checks the 'etag' specified by the server to catch unmodified
                        data.
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 807px; padding: 8px;">
            <div><span style="color: rgb(0, 0, 0); --inversion-type-color:  simple;">Callbacks</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(187, 187, 187); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">context</span></div>
        </td>
        <td style="border: 1px solid rgb(187, 187, 187); width: 677px; padding: 8px;" colspan="4">
            <div>Set the value of <span style="font-family: &quot;Courier New&quot;;">this</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;"> for callbacks</span></div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>$.ajax({</div>
                <div>  url: "test.html",</div>
                <div>  <span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">context: document.body</span>
                </div>
                <div>}).done(function() {</div>
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">  $( this )</span>.addClass(
                    "done" );
                </div>
                <div>});</div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(187, 187, 187); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">global</span></div>
        </td>
        <td style="border: 1px solid rgb(187, 187, 187); width: 677px; padding: 8px;" colspan="4">
            <div>Whether to trigger the global Ajax event handlers for this request (see below)</div>
            <ul>
                <li>
                    useful to, for example, suppress a loading indicator that was implemented with <span
                            style="font-family: &quot;Courier New&quot;;">.ajaxSend()</span> if the requests
                        are frequent and brief.
                </li>
                <li>
                    With cross-domain script and JSONP requests, the global option is automatically set
                        to <span style="font-family: &quot;Courier New&quot;;">false</span>
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(187, 187, 187); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">jsonp </span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">and </span><span
                    style="font-family: &quot;Courier New&quot;;">jsonpCallback</span></div>
        </td>
        <td style="border: 1px solid rgb(187, 187, 187); width: 677px; padding: 8px;" colspan="4">
            <div>Override the callback function name in a JSONP request (string or boolean)</div>
            <ul>
                <li>
                    value will be used instead of '<span style="font-family: &quot;Courier New&quot;;">callback</span>'
                        in the '<span style="font-family: &quot;Courier New&quot;;">callback=?'</span> part of
                        the query string in the url.
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>{ jsonp:’onJSONPLoad’ } // result in 'onJSONPLoad=?' passed to the server</div>
            </div>
            <div><br/></div>
            <div>Specify the callback function name for a JSONP request</div>
            <ul>
                <li>
                    value will be used instead of the random name automatically generated by jQuery
                </li>
                <li>
                     preferable to let jQuery generate a unique name as it'll make it easier to manage
                        the requests and provide callbacks and error handling
                </li>
                <li>
                    may want to specify the callback when you want to enable better browser caching of
                        GET requests
                </li>
                <li>
                    As of jQuery 1.5, you can also use a function for this setting, in which case the
                        value of jsonpCallback is set to the return value of that function
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>{ jsonpCallback:’foo’ }</div>
            </div>
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">beforeSend</span></div>
        </td>
        <td colspan="4" style="border: 1px solid rgb(204, 204, 204); width: 677px; padding: 8px;">
            <div>Can be used to modify the jqXHR object before it is sent</div>
            <ul>
                <li>
                    Use this to set custom headers
                </li>
                <li>
                    Returning <span style="font-family: &quot;Courier New&quot;;">false</span> will
                        cancel the request
                </li>
                <li>
                    As of jQuery 1.5, the <span
                            style="font-family: &quot;Courier New&quot;;">beforeSend</span> option will be
                        called regardless of the type of request
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>Function( jqXHR jqXHR, PlainObject settings )</div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">success</span></div>
        </td>
        <td colspan="4" style="border: 1px solid rgb(204, 204, 204); width: 677px; padding: 8px;">
            <div>Function to be called if the request succeeds</div>
            <ul>
                <li>
                    <span style="font-family: &quot;Courier New&quot;;">data</span> returned from the
                        server, formatted according to the <span
                                style="font-family: &quot;Courier New&quot;;">dataType</span> parameter or the
                        <span style="font-family: &quot;Courier New&quot;;">dataFilter</span> callback, if
                        also specified
                </li>
                <li>
                    A string describing the status
                </li>
                <li>
                    <span style="font-family: &quot;Courier New&quot;;">jqXHR</span> (in jQuery 1.4.x,
                        XMLHttpRequest) object
                </li>
                <li>
                    Can accept an array of functions to be called in series, as of jQuery 1.5
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>Function( Anything data, String textStatus, jqXHR jqXHR )</div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">error</span></div>
        </td>
        <td colspan="4" style="border: 1px solid rgb(204, 204, 204); width: 677px; padding: 8px;">
            <div> Function to be called if the request fails</div>
            <ul>
                <li>
                    textStatus can be <span style="font-family: &quot;Courier New&quot;;">null, "timeout", "error", "abort",</span>
                        or <span style="font-family: &quot;Courier New&quot;;">"parsererror"</span>
                </li>
                <li>
                    <span style="font-family: &quot;Courier New&quot;;">errorThrown</span><span
                            style="font-family: &quot;Helvetica Neue&quot;;"> receives the textual portion of the HTTP status</span>
                </li>
                <li>
                    Can accept an array of functions to be called in series, as of jQuery 1.5
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>Function( jqXHR jqXHR, String textStatus, String errorThrown )</div>
            </div>
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 130px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">complete</span></div>
        </td>
        <td colspan="4" style="border: 1px solid rgb(204, 204, 204); width: 677px; padding: 8px;">
            <div>When the request finishes (after success and error callbacks are executed)</div>
            <ul>
                <li>
                     string categorizing the status of the request (<span
                            style="font-family: &quot;Courier New&quot;;">"success", "notmodified", "nocontent", "error", "timeout", "abort",</span>
                        or <span style="font-family: &quot;Courier New&quot;;">"parsererror"</span>)
                </li>
                <li>
                    As of jQuery 1.5, the complete setting can accept an array of functions. Each
                        function will be called in turn
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>Function( jqXHR jqXHR, String textStatus )</div>
            </div>
        </td>
    </tr>
    </tbody>
</table>

## Other utilities

If you are already using jQuery, and need to support older browsers, you can often save the overhead of a polyfill or the headache of detecting browser support by using the implementations that jQuery provides.

If you don't need to support older browsers, need a polyfill for another reason, or are using some sort of JavaScript transpilation build pipeline, then going with the more native JavaScript functions may be a better idea.

### Arrays

Use jQuery's methods iterating and filtering arrays:

```javascript
// Iteration
$.each(arrayOrObject, callback);

// Transformation
$.map(arrayOrObject, callback); 

// Filter
$.grep( [ 0, 1, 2 ], function( n, index ) {
  return n > 0;
});     
        
// Concatenation
$.merge(array1, array2);
```

### Objects

Similarly, jQuery provides helpers for objects:

```javascript
// Query methods   
$.isEmptyObject({});
$.isPlainObject({});   
$.type(target)

// Merging
$.extend({}, object1, object2);
```
