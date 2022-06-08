---
layout: post
title: "Lessons learned working with Sass and CSS variables"
date: 2022-07-04 09:52:36 +0100
comments: true
categories: 
---
           
Here's how I'm currently thinking about CSS, Sass and CSS variables, and what I'd recommend. 

<!--more-->

## Design process

Ideally development of new product designs will be a collaborative process between designers and developers as they work through the process of establishing a **Design Palette**, and then a **Design Theme**, clarifying **Design Context**s and identifying all **Design Components**. 
                                                                                          
### 1. Design fundamentals

**Design fundamentals** are rules describing what makes good design, and come from physics, psychology, cognition and (perhaps most importantly) usability research. 

They apply to each of the [virtual space](/blog/2022/06/04/using-css-and-sass-to-encode-measurement-types) dimensions. For example:
                                                                              
* Layers: how many layers users can keep track of, or how they interact with each other in order to not surprise or confuse the user
* Layout and Spacing: how far items must be apart for them to be easily discernible or clickable
* Theme: what colours complement each other
* Typography: what typefaces work well together, or what font sizes effectively communicate changes in document hierarchy or structure. 
          
### 2. Design palettes

A **Design palette** takes these fundamentals and records collections of compliant values. Some examples are:

* Layers: strategies for the amount and ordering of layers
* Layout and Spacing: progressions of units that scale how close or far apart elements are
* Theme: collections of primary, secondary and tertiary colours that work well together
* Typography: specific font families that complement each other

Palettes select a handful of values that work well with each other from the large list of possible values (e.g. the 1000s of different colours or fonts available), and bundles them up together. They take a choice between a near infinite space of possible values and replace it with one between a few collections (palettes). 

They can be constructed with one of two things in mind:

* Ease of implementation: e.g. choosing units that have many common factors and are easily divisible 
* Effectiveness: e.g. colours that are visually pleasing, or that create sufficient contrast for usability.
         
### 3. Design themes & strategies

A **Design Theme** where the actual choosing of which design palette to use in an application is done.

While **Design Themes** are collections of singular values, **Design Strategies** are cross-cutting concerns or rules. For example, truncating strings when they don't fit is a _strategy_ for how to handle content that is too long. These are generally achieved in CSS with **Property Groups** - collections of one or more CSS properties that are changed together.

Together these make up the bulk of your product's _Style Guide_.
       
### 4. Design contexts          

**Design contexts** describe how or when those **Design Theme**s and **Design Strategies** are applied (e.g. perhaps the same design looks different on mobile devices than it does on larger screens). To achieve **Design contexts** that are not easily expressed in CSS alone, you can use **Display hooks** to maintain state or perform context boundary switching in JavaScript and apply updates to the DOM where CSS can "see" it (more on this below).
            
### 5. Design components

Finally, [Design Components](/blog/2022/02/07/style-your-design-elements-not-your-mark-up) express the application's behaviour in terms of elements in the design. They express there behaviour within **Design Contexts** (possibly using **Display Hooks**) and employ **Design Strategies** and set their **Property Groups** to values from the **Design Theme**.

### Project structure

This is the file structure I'd recommend to capture information as you go along:

```text
|- styles
  |- components                      # Design Components
    |- ....                          
  |- partials
    |- palettes                      # Design Palettes
      |- _colors.scss
      |- _fonts.scss
      |- _planes.scss
      |- _dimensions.scss
    |- strategies                    # Design Strategies
      |- _layers.scss
      |- _layout.scss
      |- _theme.scss
      |- _typography.scss
    |- theme                         # Design Themes
      |- _layers.scss
      |- _layout.scss
      |- _theme.scss
      |- _typography.scss
  index.scss
  variables.scss                     # CSS variables
```

Note the following:

* Sass variables are used to achieve a **Design Theme** and **Design Palette** and Sass mixins define **Design strategies** - all of which are imported and used as Sass partials 
* Design themes and strategies are organised by [virtual space](/blog/2022/06/04/using-css-and-sass-to-encode-measurement-types) dimensions (theme, typography, layers and layout)
* All variables should be [named and defined accordance with their measurement type](/blog/2022/06/04/using-css-and-sass-to-encode-measurement-types)
* **Design contexts** and CSS variables are covered later in the article

### Refactoring legacy code to introduce design constants 

Although a more difficult task, it's also possible to refactor existing code bases to be more in line with these concepts.

An example stylesheet:

```scss
body {
  color: #083e4b;
  
  caption {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }

  .button {
    padding: 8px 12px;
    text-transform: uppercase;
    
    .primary {
      background-color: #0e3a5f;
      color: #f9f9f9;
    }

    .danger {
      background-color: #843534;
      color: #f9f9f9;
    }

    .secondary {
      background-color: #12b8d5;
    }

    .cancel {
      background-color: #93a1a1;
      color: #f9f9f9;
    }
  }
  
  .nav-item {
    padding: 8px 12px;
    text-transform: uppercase;
  }
  
  .link {
    color: #12b8d5;
  }
}
```

#### Identify design strategies

The first step is to identify **Design Strategies** (which set **Property Groups**) and encoding them as Sass mixins:

```scss
// styles/partials/strategies/_theme.scss

/**
 * Complement the font colour whenever it is used on a dark background
 */
@mixin dark-background($color) {
  background-color: $color;
  color: #f9f9f9;
}

// styles/partials/strategies/_typography.scss

/**
 * Truncate overflowing text with ellipsis
 */
@mixin overflow-ellipsis {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

/**
 * Upcase text on clickable elements and apply consistent padding
 */
@mixin clickable-element {
  padding: 8px 12px;
  text-transform: uppercase;
}

// styles/index.scss
@import './partials/strategies/theme';

body {
  color: #083e4b;

  caption {
    @include ovewflow-ellipsis;
  }

  .button {
    @include clickable-element;
    
    .primary {
      @include dark-background(#0e3a5f);
    }

    .danger {
      @include dark-background(#843534);
    }

    .secondary {
      background-color: #12b8d5;
    }

    .cancel {
      background-color: #93a1a1;
    }  
  }
  
  .nav-item {
    @include clickable-element;
  }

  .link {
    color: #12b8d5;
  }
}
```

This identifies them as re-used patterns across the design and gives them a name and prominence so your development and design team can see and discuss the designs in terms of the same concepts.

This is likely the most difficult and frustrating step, depending on how consistently the application has been developed. If you get stuck, you may find it useful to actually swap the order of the first two steps. 

It's normal to encounter opportunities to simplify and consolidate and bring more consistency across the application. As patterns emerge, work with you design team to formalise and refine them.

#### Identify a design theme

The next step is to extract a **Design Theme** into Sass variables:

```scss
// styles/partials/theme/_colours.scss
$color-primary: #0e3a5f; 
$color-secondary: #12b8d5; 

$color-danger: #843534;
$color-abandonment: #93a1a1;

$font-color: #083e4b;
$font-color-complement: #f9f9f9;

// styles/partials/theme/_layout.scss
$grid-unit-400: 8px;
$grid-unit-500: 12px;

// styles/partials/strategies/_theme.scss
@import '../theme/colors';

@mixin dark-background($color) {
  background-color: $color;
  color: $font-color-complement;
}

// styles/partials/strategies/_typography.scss
@import '../theme/layout';

@mixin overflow-ellipsis {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

@mixin clickable-element {
  padding: $grid-unit-400 $grid-unit-500;
  text-transform: uppercase;
}

// styles/index.scss
@import './partials/theme/colors';
@import './partials/strategies/theme';

body {
  color: $font-color;

  caption {
    @include ovewflow-ellipsis;
  }

  .button {
    @include clickable-element;
    
    .primary {
      @include dark-background($color-primary);
    }

    .danger {
      @include dark-background($color-danger);
    }

    .secondary {
      background-color: $color-secondary;
    }

    .cancel {
      background-color: $color-abandonment;
    }  
  }
  
  .nav-item {
    @include clickable-element;
  }

  .link {
    color: $color-secondary;
  }
}
```

Whereas the introduction of **Design Strategies** identifies, labels and re-uses _strategies_ across the design, a **Design Theme** does the same for _values_.

#### Optional: Extract out a design palette

If you're refactoring a legacy code base not necessarily built from first principles, you may stop here. But in situations where you're working from designs, it's often useful to illustrate the underlying fundamentals by starting with a **Design Palette**:

```scss
// styles/partials/palette/_colours.scss
$blue-100: #083e4b;
// ...
$blue-300: #0e3a5f;
// ...
$blue-600: #12b8d5;

// styles/partials/palette/_layout.scss
$base-4-unit-1: 4px;
$base-4-unit-2: 8px;
$base-4-unit-3: 12px;
// ...

// styles/partials/theme/_colours.scss
@import '../palette/colors';

$color-primary: $blue-300; 
$color-secondary: $blue-600; 
$font-color: $blue-100;
// ...

// styles/partials/theme/_layout.scss
$grid-unit-400: $base-4-unit-2;
$grid-unit-500: $base-4-unit-3;
//...
```

Starting with (or extracting) a **Design Palette** gives further insight into why certain values have made their way into the designs, how they relate to one another, and gives clues for solving problems like needing to add new values or change existing ones.

## Working with design variables

### Design contexts and identifying independent variables

Establishing **Design Contexts** is effectively determining what variables may change at runtime (or "use time") that could effect how a design behaves. This can be things like device orientation, changing of user preferences, or user agent settings. These become your _independent variables_, and your design is expressed in terms of _dependent variables_. It's essential these relationships are understood and encoded during the implementation of the designs. For example, if the user uses a larger default font size, all quantities that should adjust to accommodate that, should be expressed _in terms of_ the default font size.

### Dynamic design mechanisms

There are broadly speaking two types of relationships between the independent variables and their outputs:

* **On/Off**: Or hard swaps between different style rules (turning some rules off and some on).
* **Scalar**: Alter values in relation to another independent variable (we don't simply flip the value on or off).

It's worth nothing On/Off can be used to enable or disable a scalar relationship.

CSS is static, but can be leveraged through one of a two types of mechanisms to achieve different behaviour depending on the circumstances:

* `@media` queries: The browser will select _which parts_ of the CSS to apply, by which `@media` queries match. This allows us to express **Design Contexts** that depend on viewport dimensions, device properties and [more](https://developer.mozilla.org/en-US/docs/Web/CSS/@media). This is an On/Off relationship.
* Relative units: Using `rem` allows us to build designs that are adaptive to a user's preferred default font size. `%` allow defining quantities relative to parent elements, and `vw`, `vh` allow values relative to the viewport size. These are scalar relationships.

What is missing from this list is **Design Contexts** that depend on user preferences: Perhaps you want to offer a dense and sparse version of the designs, or a dark or light mode (although there are newer `@media` queries that cover the latter). For this, you need to employ **Display Hooks**:

* On/Off: Use a dynamic language either at request time (e.g. Ruby, Node, C#, etc), or runtime (JavaScript), to encode design context information where CSS can "see" it and switch between stylesheets - this is usually adding properties to the DOM.
* Scalar: Use JavaScript at runtime to calculate and directly set properties of DOM elements.
             
For example, the application could offer a "Dark mode" toggle that (through JavaScript) adds a `dark-mode` class to the `body` element:

```scss
.body {
  background-color: white;
  color: dimgrey;
  
  // Lots of other light mode defaults ...
  
  .dark-mode {
    background-color: dimgrey;
    color: white;
    
    // Lots of other changes for dark mode ...
  }
}
```

### Use CSS variables to DRY up On/Off relationships

The example above for switching to dark mode is rather contrived and simple. In reality, it would likely include hundreds of lines of switching specific values on different properties. You have to write everything twice (or at least 1.5 times): once for light mode, and once for dark mode.

CSS variables allow you to cut that in half, by letting you swap the _values_ of the properties, without having to re-state those properties: 

```scss
// style/variables.scss

.body {
  --background-color: white;
  --font-color: dimgrey;

  .dark-mode {
    --background-color: dimgrey;
    --font-color: white;
  }
}

// styles/index.scss

.body {
  background-color: var(--backgroud-color);
  color: var(--font-color);
  
  // Lots of other styling in terms of the CSS variables ...
}
```
         
CSS variables can be set from within JS, or you can continue to set properties on the DOM and use that to trigger new values in CSS (as is shown above).

### Choosing which values to express as CSS variables

CSS variables are for anything that is required to express you **Design Contexts** - anything that needs to be an variable whose value can change, if a **Design Context** switch occurs.

### Use Sass variables to wrap CSS variables

The CSS variable syntax is a little awkward to work with, and over the course of development, what's actually required to be elevated from a Sass variable to a CSS one is likely to change. To minimise the use of the awkward syntax and to insulate yourself against unnecessary find-and-replaces when changes occur, use Sass variables to wrap CSS ones, and don't access them directly:

```scss
// style/variables.scss

.body {
  --background-color: white;
  --font-color: dimgrey;

  .dark-mode {
    --background-color: dimgrey;
    --font-color: white;
  }
}

// styles/partials/theme/_colors.scss
$background-color: var(--backgroud-color);
$font-color: var(--font-color);

// styles/index.scss

@import './partials/theme/colors';

.body {
  background-color: $background-color;
  color: $font-color;
  
  // Lots of other styling in terms of the CSS variables ...
}
```
