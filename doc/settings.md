# 設定

新的 Susy 語法可以用 Sass Map 或 [縮寫](doc/shorthand.md) 語法作為基本的設定。這兩種定義可以互相通用：

The new syntax for Susy is based around a number of settings that can be written either as a Sass Map or using a [shorthand](doc/shorthand.md) syntax. These two definitions are interchangeable:

```
$susy: (
  columns: 12,
  gutters: 1/4,
  math: fluid,
  output: float,
  gutter-position: inside,
);

$shorthand: 12 1/4 fluid float inside;
```

這兩種格式都可以傳遞一個參數作為 functions 或 mixins，以構成 Susy 語言。Maps 甚至可以被用來作為縮寫的一部分：

Either format can be passed as a single argument to the functions and mixins that make up the Susy language. Maps can even be used as part of the shorthand:

```
$susy: (
  columns: 12,
  gutters: .25,
  math: fluid,
);

@include layout($susy float inside);
```
除非另有說明，大部分設置都可以在全球控制（通過設置站點範圍內的默認值）或局部（傳遞給各個功能和混入）。

除非另有說明，否則大部分的設定都可適用在全球或本地
Unless otherwise noted, most settings can be controlled both globally (by setting the site-wide default) or locally (passed to individual functions and mixins).

## Global Defaults
Here are all the global Susy settings with their default values:

```
$susy: (
  flow: ltr,
  math: fluid,
  output: float,
  gutter-position: after,
  container: auto,
  container-position: center,
  columns: 4,
  gutters: .25,
  column-width: false,
  global-box-sizing: content-box,
  last-flow: to,
  debug: (
    image: hide,
    color: rgba(#66f, .25),
    output: background,
    toggle: top right,
  ),
  use-custom: (
    background-image: true,
    background-options: false,
    box-sizing: true,
    clearfix: false,
    rem: true,
  )
);
```

You can set your own global defaults, or create individual layout maps to access as needed.

For global settings, create a $susy variable with any values that you need.

```
$susy: (
  columns: 12,
  gutters: .25,
  gutter-position: inside,
)
```

## Layout
A “layout” in Susy is made up of any combination of settings. Layouts are stored as maps, but can also be written as [shorthand](doc/shorthand.md).

### Layout [function]

Convert shorthand into a map of settings.

**function**

Format:	```layout($layout)```

$layout:	[*< layout >*](doc/shorthand.md)

```
// function input
$map: layout(auto 12 .25 inside fluid isolate);

//output
$map: (
  container: auto,
  columns: 12,
  gutters: .25,
  gutter-position: inside,
  math: fluid,
  output: isolate,
);
```

This is useful any time you need to combine settings stored in different variables. It’s not possible to combine two shorthand variables:

```
// THIS WON'T WORK
$short: 12 .25 inside;
@include layout($short fluid no-gutters);
```

but it is possible to add a map into the shorthand:

```
// THIS WILL WORK
$map: layout(12 .25 inside);
@include layout($map fluid no-gutters);
```

or combine two maps:

```
$map1: 13 static;
$map2: (6em 1em) inside;
@include layout($map1 $map2);
```

### Layout [mixin]

Set a new layout as the global default.

** mixin **

Format:	```layout($layout, $clean)```

$layout:	[*< layout >*](doc/shorthand.md)

$clean:	< boolean >

```
// mixin: set a global layout
@include layout(12 1/4 inside-static);
```

By default, these new settings are added to your existing global settings. Use the $clean argument to etablish new settings from scratch.


### With Layout

Temporarily set defaults for a section of your code.

** mixin **

Format:	```with-layout($layout, $clean) { @content }```

$layout:[*< layout >*](doc/shorthand.md)

$clean:	< boolean >

@content:	Sass content block

```
@include with-layout(8 static) {
  // Temporary 8-column static grid...
}

// Global settings are restored...
```

By default, these new settings are added to your existing global settings. Use the $clean argument to etablish new settings from scratch.

### Susy-Get

** function **

Format:	```susy-get($key, $layout)```
$key:	Setting name
$layout:	[*< layout >*](doc/shorthand.md)

You can access your layout settings at any time, using the ```susy-get``` function.

```
$large: layout(80em 24 1/4 inside);
$large-container: susy-get(container, $large);
```

To access a nested setting like ```debug/image```, send the full path as a list for the ```$key``` argument.

```
$debug-image: susy-get(debug image);
```

If no setting is available (or no ```$layout``` is provided) susy-get falls back to the global user settings, and finally to the Susy default settings.


### Flow

The reading direction of your document. Layout elements will stack out in the direction of flow, unless otherwise directed.

** setting **

Key:	```flow```

Scope:	global, local

Options:	```rtl``` | ```ltr```

Default:	```ltr```


* ```ltr``` : Layout elements will flow from left to right.


* ```rtl``` : Layout elements will flow from right to left.

### Math

Susy can produce either relative widths (fluid percentages) or static widths (using given units).

** setting **

Key:	```math```

Scope:	global, local

Options:	```fluid``` | ```static```

Default:	```fluid```

* ```fluid``` :  All internal grid spans will be calculated relative to the container, and output as % values.
* ```static``` : All internal grid values will be calculated as multiples of the column-width setting. If you set column-width to 4em, your grid widths will be output as em values.
 

### Output
Susy can generate output using different layout techniques. Currently we have a float module, with an extension to handle isolation as well. In the future there could be flexbox, grid, and other output styles.

** setting **

Key:	```output```

Scope:	global, local

Options:	```float``` | ```isolate```

Default:	```float```

* ```float``` : Floats are the most common form of layout used on the web.
* ```isolate``` : Isolation is a trick developed by John Albin Wilkins to help fix sub-pixel rounding bugs in fluid, floated layouts. You can think of it like absolute positioning of floats. We find it to be very useful for spot-checking the worst rounding bugs, but we think it’s overkill as a layout technique all to itself.
 

### Container

Set the max-width of the containing element.

** setting**

Key:	container

Scope:	global, local [container only]

Options:	```< length >``` | ```auto```

Default:	```auto```

* ```< length >``` : Set any explicit length (e.g. 60em or 80%), and it will be applied directly to the container.
* ```auto``` : Susy will calculate the width of your container based on the other grid settings, or fall back to 100%.

> Warning

> For ```static``` layouts, leave ```container: auto``` and set the ```column-width``` instead. Susy will calculate the outer container width for you. Dividing columns out of a set container width would leave you open to sub-pixel errors, and no one likes sub-pixel errors.

### Container Position

Align the container relative to it’s parent element (often the viewport).

** setting **

Key:	container-position

Scope:	global, local [container only]

Options:	```left``` | ```center``` | ```right``` | ```< length >[*2]```

Default:	```center```

* ```left``` : Holds container elements flush left, with margin-left: 0; and margin-right: auto;.
* ``` center ``` : Centers the container, by setting both left and right margins to auto.
* ``` right ``` : Pushes the container flush right, with margin-right: 0; and margin-left: auto;.
* ```< length > [*2]``` : If one length is given, it will be applied to both side margins, to offset the container from the edges of the viewport. If two values are given, they will be used as left and right margins respectively.

### Columns

Establish the column-count and arrangement for a grid.

** setting **

Key:	```columns```

Scope:	global, local

Options:	```< number >``` | ```< list >```

Default:	4

* ```< number >``` : The number of columns in your layout.
* ```< list >``` : For asymmetrical grids, list the size of each column relative to the other columns, where ```1``` is a single column-unit. ```(1 2)``` would create a 2-column grid, with the second column being twice the width of the first. For a [Fibonacci](http://en.wikipedia.org/wiki/Fibonacci_number)-inspired grid, use ```(1 1 2 3 5 8 13)```.


### Gutters
Set the width of a gutter relative to columns on your grid.

** setting **

Key:	```gutters```

Scope:	global, local

Options:	```< ratio >```

Default:	```1/4```

* ``` < ratio >``` : Gutters are established as a ratio to the size of a column. The default ```1/4``` setting will create gutters one quarter the size of a column. In asymmetrical grids, this is ```1/4``` the size of a single column-unit.

If you want to set explicit column and gutter widths, write your gutters setting as ```< gutter-width >/< column-width >```. You can even leave the units attached.

```
// 70px columns, 20px gutters
$susy: (
  gutters: 20px/70px,
);
```

### Column Width

Optionaly set the explicit width of a column.

** setting **

Key:	```column-width```

Scope:	global, local

Options:	```< length >``` | ```false/null```

Default:	```false```

* ```< length >``` : The width of one column, using any valid unit. This will be used in static layouts to calculate all grid widths, but can also be used by ```fluid``` layouts to calculate an outer maximum width for the container.

* ```false/null``` : There is no need for column-width in fluid layouts unless you specifically want the container-width calculated for you.

### Gutter Position
Set how and where gutters are added to the layout, either as padding or margins on layout elements.

** setting **

Key:	gutter-position

Scope:	global, local

Options:	```before``` | ```after``` | ```split``` | ```inside``` | ```inside-static```

Default:	```after```

* ```before``` : Gutters are added as margin before a layout element, relative to the flow direction (left-margin for ltr, right-margin for rtl). The first gutter on each row will need to be removed.
* ```after``` : Gutters are added as margin after a layout element, relative to the flow direction. The last gutter on each row will need to be removed.
* ```split``` : Gutters are added as margin on both sides of a layout element, and are not removed at the edges of the grid.
* ```inside``` : Gutters are added as padding on both sides of a layout element, and are not removed at the edges of the grid.
* ```inside-static``` : Gutters are added as static padding on both sides of a layout element, even in a fluid layout context, and are not removed at the edges of the grid.


### Global Box Sizing

Tell Susy what box model is being applied globally.

** setting **

Key:	```global-box-sizing```

Scope:	global

Options:	```border-box``` | ```content-box```

Default:	```content-box```

* ```content-box``` : Browsers use the content-box model unless you specify otherwise.
* ```border-box``` : If you are using the [Paul Irish universal border-box](http://www.paulirish.com/2012/box-sizing-border-box-ftw/) technique (or something similar), you should change this setting to ```border-box```. You can also use our border-box-sizing mixin, and we’ll take care of it all for you.
* 
For more, see the [MDN box-sizing documentation](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing).

### Last Flow

The float-direction for the last element in a row when using the float output.

** setting **

Key:	```last-flow```

Scope:	global

Options:	```from``` | ```to```

Default:	```to```

* ```from``` : This is the default for all other elements in a layout. In an ```ltr``` (left-to-right) flow, the from-direction is ```left```, and this setting would float “last” elements to the left, along with the other elements.
* ```to``` : In many cases (especially with ```fluid``` grids), it can be helpful to float the last element in a row in the opposite direction.


### Debug

Susy has a few tools to help in debugging your layout as you work. These settings help you control the debugging environment.

** setting block **

Key:	```debug```

Scope:	global, local [container only]

Options:	< map of sub-settings >

```
$susy: (
  debug: (
    image: show,
    color: blue,
    output: overlay,
    toggle: top right,
  ),
);
```

> Warning

> Grid images are not exact. Browsers have extra trouble with sub-pixel rounding on background images. These are meant for rough debugging, not for pixel-perfect measurements. Expect the ```to``` side of your grid image (```right``` if your flow is ```ltr```) to be off by several pixels.

### Debug Image

Toggle the available grid images on and off.

** setting **

Key:	```debug image```

Scope:	global, local [container only]

Options:	```show``` | ```hide``` | ```show-columns``` | ```show-baseline```

Default:	```hide```

* ```show``` : Show grid images, usually on the background of container elements, for the purpose of debugging. If you are using [Compass vertical rhythms](http://compass-style.org/reference/compass/typography/vertical_rhythm/) (or have set your own ```$base-line-height``` variable) Susy will show baseline grids as well.
* ```hide``` :     Hide all grid debugging images.
* ```show-columns``` : Show only horizontal grid-columns, even if a baseline grid is available.
* ```show-baseline``` : Show only the baseline grid, if the ```$base-line-height``` variable is available.

### Debug Output

The debug image can be output either as a background on the container, or as a generated overlay.

** setting **

Key:	```debug output```

Scope:	global, local [container only]

Options:	```background``` | ```overlay```

Default:	```background```

* ```background``` : Debugging images will be generated on on the background of the container element.
* ```overlay``` : Debugging images will be generated as an overlay using the container’s ```::before``` element. By default, the overlay is hidden, but we also generate a [toggle](http://susydocs.oddbird.net/en/latest/settings/#settings-debug-toggle) in a corner of the viewport. Hover over the toggle to make the overlay appear.

### Debug Toggle
If you are using the grid overlay option, Susy will generate a toggle to show/hide the overlay. Hovering over the toggle will show the overlay. You can place the toggle in any corner of the viewport using a combination of ```top```, ```right```, ```bottom```, and ```left```.

** setting **

Key:	```debug toggle```

Scope:	global

Options:	```right``` | ```left and top``` | ```bottom```

Default:	```top right```

### Debug Color

Change the color of columns in the generated grid image.

** setting **

Key:	debug color

Scope:	global

Options:	< color >

Default:	```rgba(#66f, .25)```

### Custom Support

There are several common helpers that you can tell Susy to use, if you provide them yourself or through a third-party library like Compass or Bourbon.

### Custom Clearfix

Tell Susy to use a global clearfix mixin.

** setting **

Key:	```use-custom clearfix```

Scope:	global

Options:	< boolean >

Default:	```false```

* ```false``` : Susy will use an internal micro-clearfix.
* ```true```: Susy will look for an existing ```clearfix``` mixin, and fallback to the internal micro-clearfix if none is found.

### Custom Background Image

Tell Susy to use a global background-image mixin. This is only used for debugging.

** setting **

Key:	```use-custom background-image```

Scope:	global

Options:	< boolean >

Default:	```true```

* ```false``` : Susy will output background-images directly to CSS.
*```true``` : Susy will look for an existing ```background-image``` mixin (like the ones provided by Compass and Bourbon), and fallback to plain CSS output if none is found.

### Custom Background Options
Tell Susy to use global ```background-size```, ```-origin```, and ```-clip``` mixins. This is only used for debugging.

** setting **

Key:	```use-custom background-options```

Scope:	global

Options:	< boolean >

Default:	```false```

* ```false``` : Susy will output background-options directly to CSS.
*```true``` : Susy will look for existing ```background-size```, ```-origin```, and ```-clip``` mixins (like the ones provided by Compass and Bourbon), and fallback to plain CSS output if none is found.

### Custom Breakpoint Options

Tell Susy to use a custom ```breakpoint``` mixin, like the one provided by the Breakpoint plugin.

** setting **

Key:	```use-custom breakpoint```

Scope:	global

Options:	< boolean >

Default:	true

* ```false``` : Susy will use an internal fallback for media-queries.
* ```true``` : Susy will look for existing an breakpoint mixin like the one provided by the [Breakpoint](http://breakpoint-sass.com) plugin, and fallback to internal media-query support if none is found.

### Custom Box Sizing

Tell Susy to use a global ```box-sizing``` mixin.

** setting **

Key:	```use-custom box-sizing```

Scope:	global

Options:	< boolean >

Default:	```true```

*```false``` : Susy will output ```box-sizing``` official syntax, as well as ```-moz``` and ```-webkit``` prefixed versions.
*```true``` : Susy will look for an existing ```box-sizing``` mixin (like the ones provided by Compass and Bourbon), and fallback to mozilla, webkit, and official syntax if none is found.

### Custom Rem

Tell Susy to use the Compass rem support module.

** setting **

Key:	```use-custom rem```

Scope:	global

Options:	< boolean >

Default:	```true```

* ```false``` : Susy will output length values directly to CSS.
* ```true``` : Susy will look for an existing ```rem``` mixin, and check the ```$rhythm-unit``` and ```$rem-with-px-fallback``` settings provided by Compass, or fallback to plain CSS output if they aren’t found.

### Location

Reference a specific column on the grid for row edges, isolation, or asymmetrical layouts. Locations keywords don’t require the ```at``` flag.

** setting **

Key:	```location```

Scope:	local

Options:	```first/alpha``` | ```last/omega``` | ```< number >```

Default:	null

* ```first```, ```alpha``` : Set location to 1.
* ```last```, ```omega``` : Set the location to the final column, and any previous columns included by the relevant span.
* ```< number >``` : Set the location to any column-index between 1 and the total number of available columns.

### Box Sizing

Set a new box model on any given element.

** setting **

Key:	```box-sizing```

Scope:	local

Options:	```border-box``` | ```content-box```

Default:	```null```

* ```border-box``` : Output ```box-sizing``` CSS to set the ```border-box``` model.
*```content-box``` : Output ```box-sizing``` CSS to set the ```content-box``` model.

### Spread

Adjust how many gutters are included in a column span.

** setting **

Key:	```spread```

Scope:	local

Options:	```narrow``` | ```wide``` | ```wider```

Default:	various...

* ```narrow``` : In most cases, column-spans include the gutters between columns. A span of ```3 narrow``` covers the width of 3 columns, as well as 2 internal gutters. This is the default in most cases.
* ```wide``` : Sometimes you need to include one side gutter in a span width. A span of ```3 wide``` covers the width of 3 columns, and 3 gutters (2 internal, and 1 side). This is the default for several margin/padding mixins.
*```wider``` : Sometimes you need to include both side gutters in a span width. A span of ```3 wider``` covers the width of 3 columns, and 4 gutters (2 internal, and 2 sides).

### Gutter Override

Set an explicit one-off gutter-width on an element, or remove its gutters entirely.

** setting **

Key:	```gutter-override```

Scope:	local

Options:	```no-gutters/no-gutter``` | ```< length >```

Default:	```null```

* ```no-gutters```, ```no-gutter``` : Remove all gutter output.
* ```< length >``` : Override the calculated gutter output with an explicit width.

### Role

Mark a grid element as a nesting context for child elements.

** setting **

Key:	```role```

Scope:	local

Options:	```nest```

Default:	```null```

* ```nest``` : Mark an internal grid element as a context for nested grids.

> Note

> This can be used with any grid type, but it is required for nesting with ```split```, ```inside```, or ```inside-static``` gutters.