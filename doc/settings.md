# Settings

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

Either format can be passed as a single argument to the functions and mixins that make up the Susy language. Maps can even be used as part of the shorthand:

```
$susy: (
  columns: 12,
  gutters: .25,
  math: fluid,
);

@include layout($susy float inside);
```

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
告訴Susy該應用哪種盒模型在全域上。   
** setting **

關鍵字:	```global-box-sizing```

範圍:	global

選項:	```border-box``` | ```content-box```

預設:	```content-box```

* ```content-box``` : 瀏覽器使用content-box來建立盒模型，除非你手動指定其它設定。Browsers use the content-box model unless you specify otherwise.
* ```border-box``` : 假使你是使用[Paul Irish universal border-box](http://www.paulirish.com/2012/box-sizing-border-box-ftw/)(或其他相似的技術)，你應該將它設定為```border-box```。你也可以使用我們的border-box-sizing mixin，我們會全部為你打理好。
If you are using the [Paul Irish universal border-box](http://www.paulirish.com/2012/box-sizing-border-box-ftw/) technique (or something similar), you should change this setting to ```border-box```. You can also use our border-box-sizing mixin, and we’ll take care of it all for you.
* 更多的資訊你可以瀏覽這篇文章：[MDN box-sizing documentation](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)。For more, see the [MDN box-sizing documentation](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing).

### Last Flow

當使用float輸出時，可控制row裡面的last元素的浮動方向。
The float-direction for the last element in a row when using the float output.

** setting **

關鍵字:	```last-flow```

範圍:	global

選項:	```from``` | ```to```

預設:	```to```

* ```from``` : 預設layout裡面的所有其他元素皆是```ltr``` (由左至右)來排列，float方向會從左邊開始。而這個設定會讓“last”元素靠左，同時連同著其他元素。
This is the default for all other elements in a layout. In an ```ltr``` (left-to-right) flow, the from-direction is ```left```, and this setting would float “last” elements to the left, along with the other elements.
* ```to``` :在許多情況下(尤其是```fulid``` grids)，它可以幫助你控制row裡面的last元素的float方向設為相反的方向。
 In many cases (especially with ```fluid``` grids), it can be helpful to float the last element in a row in the opposite direction.


### Debug

Susy有一些工具可以幫助你的Layout進行除錯. 這些設定能夠控制除錯環境。Susy has a few tools to help in debugging your layout as you work. These settings help you control the debugging environment.


** setting block **

關鍵字:	```debug```

範圍:	global, local [container only]

選項:	< map of sub-settings >

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
注意
> Grid images並不精準，瀏覽器在background images上會有額外sub-pixel的麻煩，這些只提供簡單的測試，像素上並非完美精準，使用```to```側在grid image(```right```如果你的flow是```ltr```)，它將會減少幾個像素。 Grid images are not exact. Browsers have extra trouble with sub-pixel rounding on background images. These are meant for rough debugging, not for pixel-perfect measurements. Expect the ```to``` side of your grid image (```right``` if your flow is ```ltr```) to be off by several pixels.



### Debug Image

Toggle the available grid images on and off.  
切換grid images開啟或關閉
** setting **

關鍵字:	```debug image```

範圍:	global, local [container only]

選項:	```show``` | ```hide``` | ```show-columns``` | ```show-baseline```

預設:	```hide```

* ```show``` : 顯示grid images，會出現在容器元素的背景上來進行除錯，如果你使用 [Compass vertical rhythms](http://compass-style.org/reference/compass/typography/vertical_rhythm/) (或者設定你自己的```$base-line-height```變數，Susy也會顯示baseline grids )。Show grid images, usually on the background of container elements, for the purpose of debugging. If you are using [Compass vertical rhythms](http://compass-style.org/reference/compass/typography/vertical_rhythm/) (or have set your own ```$base-line-height``` variable) Susy will show baseline grids as well.
* ```hide``` :隱藏全部除錯用的grid images.Hide all grid debugging images.
* ```show-columns``` : 只顯示橫向的grid-columns，即使baseline grid是存在的。Show only horizontal grid-columns, even if a baseline grid is available.
* ```show-baseline``` : 顯示baseline grid，如果```$base-line-height```變數是存在的。Show only the baseline grid, if the ```$base-line-height``` variable is available.

### Debug Output

The debug image can be output either as a background on the container, or as a generated overlay.
讓除錯影像能夠輸出背景在container上，或客製在overlay上
** setting **

關鍵字:	```debug output```

範圍:	global, local [container only]

選項:	```background``` | ```overlay```

預設:	```background```

* ```background``` : 除錯影像會產生在container元素的背景上。Debugging images will be generated on on the background of the container element.
* ```overlay``` : 除錯影響會產生在container的```::before```元素上，在預設情況下，overlay會隱藏起來，但會產生[切換]功能在角落的viewport上，當滑鼠滑曳過去時便會顯示。 Debugging images will be generated as an overlay using the container’s ```::before``` element. By default, the overlay is hidden, but we also generate a [toggle](http://susydocs.oddbird.net/en/latest/settings/#settings-debug-toggle) in a corner of the viewport. Hover over the toggle to make the overlay appear.

### Debug Toggle
如果你使用grid overlay的選項，Susy會產生一個可以切換顯示/關閉的功能在overlay上。當滑鼠滑過去便顯示。你可以切換在任何角落於viewport上，其中包含```top```、```right```、```bottom```、```left```。If you are using the grid overlay option, Susy will generate a toggle to show/hide the overlay. Hovering over the toggle will show the overlay. You can place the toggle in any corner of the viewport using a combination of ```top```, ```right```, ```bottom```, and ```left```.


** setting **

關鍵字:	```debug toggle```

範圍:	global

選項:	```right``` | ```left and top``` | ```bottom```

預設:	```top right```

### Debug Color

產生grid image的columns顏色，Change the color of columns in the generated grid image.

** setting **

關鍵字:	debug color

範圍:	global

選項:	< color >

預設:	```rgba(#66f, .25)```

### Custom Support

Susy提供了一些常見的helpers功能，你可以使用它們，或者使用第三方libary，像是Compass或Bourbon。There are several common helpers that you can tell Susy to use, if you provide them yourself or through a third-party library like Compass or Bourbon.

### Custom Clearfix

告訴Susy使用全域清除浮動的mixin。Tell Susy to use a global clearfix mixin.

** setting **

關鍵字:	```use-custom clearfix```

範圍:	global

選項:	< boolean >

預設:	```false```

* ```false``` : Susy會使用micro-clearfix清除內部浮動的方法。Susy will use an internal micro-clearfix.
* ```true```: Susy會尋找現有的```clearfix```mixin，如果沒找到就會回傳micro-clearfix清除內部浮動的方法。Susy will look for an existing ```clearfix``` mixin, and fallback to the internal micro-clearfix if none is found.

### Custom Background Image

告訴Susy使用全域background-image mixin，這僅用於在除錯上。Tell Susy to use a global background-image mixin. This is only used for debugging.

** setting **

關鍵字:	```use-custom background-image```

範圍:	global

選項:	< boolean >

預設:	```true```

* ```false``` : Susy會直接輸出background images在CSS。Susy will output background-images directly to CSS.
*```true``` : Susy會尋找現有的```background-image```mixin(像是Compass或Bourbon其中一種)，如果沒找到就回傳普通的CSS輸出。Susy will look for an existing ```background-image``` mixin (like the ones provided by Compass and Bourbon), and fallback to plain CSS output if none is found.

### Custom Background Options
告訴Susy使用全域```background-size```, ```-origin```, and ```-clip``` mixins，這僅用於在除錯上。Tell Susy to use global ```background-size```, ```-origin```, and ```-clip``` mixins. This is only used for debugging.
** setting **

關鍵字:	```use-custom background-options```

範圍:	global

選項:	< boolean >

預設:	```false```

* ```false``` : Susy會輸出background-options在CSS上。Susy will output background-options directly to CSS.
*```true``` : Susy會尋找現有的```background-size```, ```-origin```, and ```-clip``` mixins，(像是Compass或Bourbon其中一種)，如果沒找到就回傳普通的CSS輸出。Susy will look for existing ```background-size```, ```-origin```, and ```-clip``` mixins (like the ones provided by Compass and Bourbon), and fallback to plain CSS output if none is found.

### Custom Breakpoint Options

告訴Susy使用客製breakpoint mixin，像是提供一個斷點plugin。Tell Susy to use a custom ```breakpoint``` mixin, like the one provided by the Breakpoint plugin.

** setting **

關鍵字:	```use-custom breakpoint```

範圍:	global

選項:	< boolean >

預設:	true

* ```false``` :Susy會回傳一個內部的media-queries。 Susy will use an internal fallback for media-queries.
* ```true``` : Susy會尋找現有的breakpoint mixin，像是[Breakpoint](http://breakpoint-sass.com) plugin，如果沒找到就回傳內部支持的media-query。Susy will look for existing an breakpoint mixin like the one provided by the [Breakpoint](http://breakpoint-sass.com) plugin, and fallback to internal media-query support if none is found.

### Custom Box Sizing

告訴Susy使用全域```box-sizing``` mixin設定。Tell Susy to use a global ```box-sizing``` mixin.

** setting **

關鍵字:	```use-custom box-sizing```

範圍:	global

選項:	< boolean >

預設:	```true```

*```false``` : Susy會輸出```box-sizing```官方語法，以及```-moz``` and ```-webkit```的前綴詞版本。Susy will output ```box-sizing``` official syntax, as well as ```-moz``` and ```-webkit``` prefixed versions.
*```true``` : Susy會尋找現有的existing ```box-sizing``` mixin(像是Compass或Bourbon其中一種)，如果沒找到就會回傳mozilla、webkit及官方語法。Susy will look for an existing ```box-sizing``` mixin (like the ones provided by Compass and Bourbon), and fallback to mozilla, webkit, and official syntax if none is found.

### Custom Rem

告訴Susy使用Compass rem support模組。Tell Susy to use the Compass rem support module.

** setting **

關鍵字:	```use-custom rem```

範圍:	global

選項:	< boolean >

預設:	```true```

* ```false``` : Susy會輸出長度值在CSS上。Susy will output length values directly to CSS.
* ```true``` : Susy會尋找現有的```rem``` mixin，然後檢查Compass的```$rhythm-unit```跟```$rem-with-px-fallback```設定，如果沒找到就回傳普通的CSS輸出。Susy will look for an existing ```rem``` mixin, and check the ```$rhythm-unit``` and ```$rem-with-px-fallback``` settings provided by Compass, or fallback to plain CSS output if they aren’t found.

### Location

引用一個特定欄的grid在row edges, isolation, or asymmetrical layouts上，Locations關鍵字不需要```at```的標誌。Reference a specific column on the grid for row edges, isolation, or asymmetrical layouts. Locations keywords don’t require the ```at``` flag.

** setting **

關鍵字:	```location```

範圍:	local

選項:	```first/alpha``` | ```last/omega``` | ```< number >```

預設:	null

* ```first```, ```alpha``` : 定位在1。Set location to 1.
* ```last```, ```omega``` : 定位在最後一欄，任何前面的欄數包含相關的span。Set the location to the final column, and any previous columns included by the relevant span.
* ```< number >``` : 設定在任何一個column-index介於1至columns總數。Set the location to any column-index between 1 and the total number of available columns.

### Box Sizing

設定新的盒模型在任何元素上。Set a new box model on any given element.
** setting **

關鍵字:	```box-sizing```

範圍:	local

選項:	```border-box``` | ```content-box```

預設:	```null```

* ```border-box``` :輸出```box-sizing``` CSS並設置為```border-box```模型。 Output ```box-sizing``` CSS to set the ```border-box``` model.
*```content-box``` : 輸出```box-sizing``` CSS並設置為```content-box```模型。Output ```box-sizing``` CSS to set the ```content-box``` model.

### Spread

調整有多少gutters包含在一個column span上。Adjust how many gutters are included in a column span.

** setting **

關鍵字:	```spread```

範圍:	local

選項:	```narrow``` | ```wide``` | ```wider```

預設:	various...

* ```narrow``` : 絕大多數情況下，column-spans包括columns之間的gutters，如果span是```3 narrow```，那寬度便涵蓋了3個columns以及2個內部gutters。這是預設在大多數情況下。 In most cases, column-spans include the gutters between columns. A span of ```3 narrow``` covers the width of 3 columns, as well as 2 internal gutters. This is the default in most cases.
* ```wide``` : 有些時候你需要包含1個外側gutter在一個span寬度，如果span是```3 narrow```，那寬度便涵蓋了3個columns與3個gutters(兩個內部跟一個外側)。這是預設的幾個margin/padding mixins。Sometimes you need to include one side gutter in a span width. A span of ```3 wide``` covers the width of 3 columns, and 3 gutters (2 internal, and 1 side). This is the default for several margin/padding mixins.
*```wider``` : 有些時候你需要包含兩邊外側的gutters在一個span寬度，如果span是```3 wider```，那寬度便涵蓋了3個columns與4個gutters(2個內部跟2邊外側)Sometimes you need to include both side gutters in a span width. A span of ```3 wider``` covers the width of 3 columns, and 4 gutters (2 internal, and 2 sides).

### Gutter Override

設定gutter寬度在元素上，或完全移除gutter。Set an explicit one-off gutter-width on an element, or remove its gutters entirely.

** setting **

關鍵字:	```gutter-override```

範圍:	local

選項:	```no-gutters/no-gutter``` | ```< length >```

預設:	```null```

* ```no-gutters```, ```no-gutter``` : 移除所有gutter輸出。Remove all gutter output.
* ```< length >``` : 覆蓋計算出來的gutter並輸出明確的寬度。Override the calculated gutter output with an explicit width.

### Role

標示一個grid元素在嵌套的子元素上。Mark a grid element as a nesting context for child elements.

** setting **

關鍵字:	```role```

範圍:	local

選項:	```nest```

預設:	```null```

* ```nest``` : 標示一個內部grid元素在一個嵌套的Grid上面。Mark an internal grid element as a context for nested grids.

> Note
注意
> This can be used with any grid type, but it is required for nesting with ```split```, ```inside```, or ```inside-static``` gutters.
> 這可以使用在任何一種grid種類，但是它需要嵌套在```split```、```inside```或```inside-static``` gutters。