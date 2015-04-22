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

除非另有說明，否則大部分的設定都可適用於全域(透過設定網站範圍內的預設值)或本地(傳遞個別的 functions 以及 mixins)

Unless otherwise noted, most settings can be controlled both globally (by setting the site-wide default) or locally (passed to individual functions and mixins).

## 全域的預設值 Global Defaults

以下是 Susy 所有全域設定的預設值：

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

你可以參考上表設定你自己的全域設定，或根據您的需求，新增一個獨立的 layout maps 以供存取。

You can set your own global defaults, or create individual layout maps to access as needed.

在全域設定裡，可以新增一個 ```$susy``` 的變數，並寫入任何你需要的數值。

For global settings, create a $susy variable with any values that you need.

```
$susy: (
  columns: 12,
  gutters: .25,
  gutter-position: inside,
)
```

## Layout

在 Susy 裡，「Layout」是作為各種佈局組合的設定。Layouts 可以被當作 maps 儲存，也可以用各種 [縮寫](doc/shorthand.md)表示

A “layout” in Susy is made up of any combination of settings. Layouts are stored as maps, but can also be written as [shorthand](doc/shorthand.md).

### Layout [function]

將縮寫轉換成 map 的各種設定

Convert shorthand into a map of settings.

**function**

格式 Format:	```layout($layout)```

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

如果要將各種不同的變數組合在一起，這在任何時候都很非常有用。但我們不能組合兩種 ```shortand``` 的變數：

This is useful any time you need to combine settings stored in different variables. It’s not possible to combine two shorthand variables:

```
// THIS WON'T WORK
$short: 12 .25 inside;
@include layout($short fluid no-gutters);
```

但我們可以將 ```map``` 加到 ```shortant``` 裡：

but it is possible to add a map into the shorthand:

```
// THIS WILL WORK
$map: layout(12 .25 inside);
@include layout($map fluid no-gutters);
```

或是合併兩種 ```maps```：

or combine two maps:

```
$map1: 13 static;
$map2: (6em 1em) inside;
@include layout($map1 $map2);
```

### Layout [mixin]

設定一個新的 layout，作為全域的預設值

Set a new layout as the global default.

** mixin **

格式 Format:	```layout($layout, $clean)```

$layout:	[*< layout >*](doc/shorthand.md)

$clean:	< boolean >

```
// mixin: set a global layout
@include layout(12 1/4 inside-static);
```

預設情況下，這些新的設定都會增加到你專案現有的全域設定中。使用 ```$clean``` 變數以建立一個新的設定。

By default, these new settings are added to your existing global settings. Use the $clean argument to etablish new settings from scratch.


### With Layout

暫時將一段程式碼設定成預設值

Temporarily set defaults for a section of your code.

** mixin **

格式 Format:	```with-layout($layout, $clean) { @content }```

$layout:[*< layout >*](doc/shorthand.md)

$clean:	< boolean >

@content:	Sass content block

```
@include with-layout(8 static) {
  //暫時使用 8-column static grid...
}

// 回到全域設定 ... Global settings are restored...
```

預設情況下，這些新的設定都會增加到你專案現有的全域設定中。使用 ```$clean``` 變數以建立一個新的設定。

By default, these new settings are added to your existing global settings. Use the $clean argument to etablish new settings from scratch.

### Susy-Get

** function **

格式 Format:	```susy-get($key, $layout)```

$key:	Setting name

$layout:	[*< layout >*](doc/shorthand.md)

可以使用 ```susy-get``` 函式，在任何時候存取你的 layout 設定。

You can access your layout settings at any time, using the ```susy-get``` function.

```
$large: layout(80em 24 1/4 inside);
$large-container: susy-get(container, $large);
```

如要存取像 ```debug/image``` 這類的巢狀設定，必須傳送完整的路徑作為 ```$key``` 的參數列表。

To access a nested setting like ```debug/image```, send the full path as a list for the ```$key``` argument.

```
$debug-image: susy-get(debug image);
```

如果沒有任何設定可用(或是未提供 ```$layout```)，```susy-get``` 會回傳使用者的全域設定，最後到 Susy 預設設定。

If no setting is available (or no ```$layout``` is provided) susy-get falls back to the global user settings, and finally to the Susy default settings.


### Flow

你的文件閱讀的方向。除非另外設定，否則 Layout 元素會以閱讀的方向流動堆疊。

The reading direction of your document. Layout elements will stack out in the direction of flow, unless otherwise directed.

** 設定 setting **

關鍵字 Key:	```flow```

範圍 Scope:	global, local

選項 Options:	```rtl``` | ```ltr```

預設 Default:	```ltr```


* ```ltr``` : Layout 元素的流動會從左至右 Layout elements will flow from left to right.


* ```rtl``` : Layout 元素的流動會從右至左 Layout elements will flow from right to left.

### Math

Susy 可以產生相對寬度(流動式百分比)或固定寬度(使用固定單位)

Susy can produce either relative widths (fluid percentages) or static widths (using given units).

** 設定 setting **

關鍵字 Key:	```math```

範圍 Scope:	global, local

選項 Options:	```fluid``` | ```static```

預設 Default:	```fluid```

* ```fluid``` : 所有內部 grid 的範圍值，是相對於 container 以計算出來的，輸出為 % 單位。 All internal grid spans will be calculated relative to the container, and output as % values.

* ```static``` : 所有內部 grid 的範圍值，會是欄寬的倍數。如果你設定欄寬為 4em，那 grid 的寬度也會以 em 輸出。 All internal grid values will be calculated as multiples of the column-width setting. If you set column-width to 4em, your grid widths will be output as em values.
 

### Output

Susy 可以使用不同的 layout 技術產生 ```output```。目前我們有一個浮動模組，可以擴充他以處理各種區塊定位。未來有可能是 flexbox, grid，或是其他 output 樣式。

Susy can generate output using different layout techniques. Currently we have a float module, with an extension to handle isolation as well. In the future there could be flexbox, grid, and other output styles.

** 設定 setting **

關鍵字:	```output```

範圍:	global, local

選項:	```float``` | ```isolate```

預設:	```float```


* ```float``` : float 是最常見的網站 layout 技術。Floats are the most common form of layout used on the web.
* ```isolate``` : John Albin Wilkins 發明了 isolation 這個技巧，當初是為了解決流動式佈局中，sub-pixel 在計算時因為四捨五入而產生的 bug。你可以把它想成是浮動的絕對定位。我們發現他可以有效解決四捨五入的問題，但我們覺得這個技術有點矯枉過正。

* Isolation is a trick developed by John Albin Wilkins to help fix sub-pixel rounding bugs in fluid, floated layouts. You can think of it like absolute positioning of floats. We find it to be very useful for spot-checking the worst rounding bugs, but we think it’s overkill as a layout technique all to itself.
 

### Container

在容器元素設定最大寬度

Set the max-width of the containing element.

** 設定 setting**

關鍵字 Key:	container

範圍 Scope:	global, local [container only]

選項 Options:	```< length >``` | ```auto```

預設 Default:	```auto```

* ```< length >``` : 設定一個確定的數值(例如 60em 或 80%)，此數值會被套用在所有容器上。Set any explicit length (e.g. 60em or 80%), and it will be applied directly to the container.
* ```auto``` : Susy 會根據其他 grid 的設定自動計算容器的寬度，或是直接回傳 100%。 Susy will calculate the width of your container based on the other grid settings, or fall back to 100%

> 注意 Warning

> 如果你使用 ```static``` layout，請用 ```container: auto``` 以及 ```column-width``` 取代。Susy 會自動計算容器外框的寬度。將容器寬度分割成欄寬，可以避免 sub-pixel 產生的問題。

> For ```static``` layouts, leave ```container: auto``` and set the ```column-width``` instead. Susy will calculate the outer container width for you. Dividing columns out of a set container width would leave you open to sub-pixel errors, and no one likes sub-pixel errors.

### Container Position

以父元素(通常是可視區)為基準對齊該容器。

Align the container relative to it’s parent element (often the viewport).

** 設定 setting **

關鍵字 Key:	container-position

範圍 Scope:	global, local [container only]

選項 Options:	```left``` | ```center``` | ```right``` | ```< length >[*2]```

預設 Default:	```center```

* ```left``` : 用 ```margin-left: 0;``` 以及 ```margin-right: auto;``` 讓容器元素靠左對齊。
 Holds container elements flush left, with margin-left: 0; and margin-right: auto;.
* ``` center ``` : 設定 margin 的左右兩邊為 auto，讓容器置中。 Centers the container, by setting both left and right margins to auto.
* ``` right ``` : 用 ```margin-right: 0;``` 以及 ```margin-left: auto;``` 讓容器元素靠右對齊。Pushes the container flush right, with margin-right: 0; and margin-left: auto;.
* ```< length > [*2]``` : 如果只給一個值，會從可視區域的邊緣開始推移兩側的 margin。如果設定兩個值，會被視為左右 margin。 If one length is given, it will be applied to both side margins, to offset the container from the edges of the viewport. If two values are given, they will be used as left and right margins respectively.

### Columns

建立欄寬數量並做成一個 grid。

Establish the column-count and arrangement for a grid.

** 設定 setting **

關鍵字 Key:	```columns```

範圍 Scope:	global, local

選項 Options:	```< number >``` | ```< list >```

預設 Default:	4

* ```< number >``` : 設定 layout 的欄寬數量。  The number of columns in your layout.
* ```< list >``` : 用來設定不對稱的 grid ，每一列的設定都相對於其他列。假設 ```1``` 代表一個單一的欄寬， 那 ```(1 2)```就會建立一個擁有 2 個欄寬的 grid，第 2 個欄寬的寬度是第 1 個的兩倍。使用 ```(1 1 2 3 5 8 13)``` 的概念來自於 [Fibonacci](http://en.wikipedia.org/wiki/Fibonacci_number)。For asymmetrical grids, list the size of each column relative to the other columns, where ```1``` is a single column-unit. ```(1 2)``` would create a 2-column grid, with the second column being twice the width of the first. For a [Fibonacci](http://en.wikipedia.org/wiki/Fibonacci_number)-inspired grid, use ```(1 1 2 3 5 8 13)```.

### Gutters

在 grid 裡設定相對於欄寬的 gutter 寬度

Set the width of a gutter relative to columns on your grid.

** 設定 setting **

關鍵字 Key:	```gutters```

範圍 Scope:	global, local

選項 Options:	```< ratio >```

預設 Default:	```1/4```

* ``` < ratio >``` : Gutter 用來作為各個 column 的比例。預設值是 ```1/4```，表示 gutter 的寬度是 column 的四分之一。在不對稱的 grid 中，```1/4``` 表示的是單一個的 column 單元。 Gutters are established as a ratio to the size of a column. The default ```1/4``` setting will create gutters one quarter the size of a column. In asymmetrical grids, this is ```1/4``` the size of a single column-unit.

如果要設定精確的 column 以及 gutter 寬度，可以寫成 ```< gutter-width >/< column-width >```，甚至可以寫上單位。If you want to set explicit column and gutter widths, write your gutters setting as ```< gutter-width >/< column-width >```. You can even leave the units attached.

```
// 70px columns, 20px gutters
$susy: (
  gutters: 20px/70px,
);
```

### Column Width

可以自由設定 column 的寬度。

Optionaly set the explicit width of a column.

** 設定 setting **

關鍵字 Key:	```column-width```

範圍 Scope:	global, local

選項 Options:	```< length >``` | ```false/null```

預設 Default:	```false```

* ```< length >``` : 可以用各種單位設定 column 的寬度。他可以在固定的 layout 算出所有 grid 的寬度，但如果設定 ```fluide``` layout，也可以用它能算出容器外框的最大寬度。 The width of one column, using any valid unit. This will be used in static layouts to calculate all grid widths, but can also be used by ```fluid``` layouts to calculate an outer maximum width for the container.

* ```false/null``` : 除非需要特別計算 container-width，否則沒有必要在 fluid layout 設定。There is no need for column-width in fluid layouts unless you specifically want the container-width calculated for you.

### Gutter Position

在 layout 設定如何顯示或排列 gutter 位置，有可能是用 padding 或是 margin

Set how and where gutters are added to the layout, either as padding or margins on layout elements.

** 設定 setting **

關鍵字 Key:	gutter-position

範圍 Scope:	global, local

選項 Options:	```before``` | ```after``` | ```split``` | ```inside``` | ```inside-static```

預設 Default:	```after```

* ```before``` : Gutters 會放在相對於流動方向的 layout 元素的前面，並用 margin 表示 (left-margin 是 ltr，right-margin 是 rtl)。每一列的第一個 gutter 都會被移除。 Gutters are added as margin before a layout element, relative to the flow direction (left-margin for ltr, right-margin for rtl). The first gutter on each row will need to be removed.
* ```after``` : Gutters 會放在相對於流動方向的 layout 元素的後面，並用 margin 表示。每一列的最後一個 gutter 都會被移除。Gutters are added as margin after a layout element, relative to the flow direction. The last gutter on each row will need to be removed.
* ```split``` : Gutter 會放在 layout 元素的兩側，並用 margin 表示，而且不會移除邊界的 grid。Gutters are added as margin on both sides of a layout element, and are not removed at the edges of the grid.
* ```inside``` : Gutter 會放在 layout 元素的兩側，並用 padding 表示，而且不會移除邊界的 grid。Gutters are added as padding on both sides of a layout element, and are not removed at the edges of the grid.
* ```inside-static``` : Gutter 會放在 layout 元素的兩側，流動式佈局也適用，並用 padding 表示，而且不會移除邊界的 grid。Gutters are added as static padding on both sides of a layout element, even in a fluid layout context, and are not removed at the edges of the grid.


### Global Box Sizing

Tell Susy what box model is being applied globally.  
告訴 Susy 該應用哪種盒模型在全域上。   
** 設定 **

關鍵字:	```global-box-sizing```

範圍:	global

選項:	```border-box``` | ```content-box```

預設:	```content-box```

* ```content-box``` : 瀏覽器使用 content-box 來建立盒模型，除非你手動指定其它設定。Browsers use the content-box model unless you specify otherwise.
* ```border-box``` : 假使你是使用[Paul Irish universal border-box](http://www.paulirish.com/2012/box-sizing-border-box-ftw/)(或其他相似的技術)，你應該將它設定為```border-box```。你也可以使用我們的 border-box-sizing mixin，我們會全部為你打理好。
If you are using the [Paul Irish universal border-box](http://www.paulirish.com/2012/box-sizing-border-box-ftw/) technique (or something similar), you should change this setting to ```border-box```. You can also use our border-box-sizing mixin, and we’ll take care of it all for you.
* 更多的資訊你可以瀏覽這篇文章：[MDN box-sizing documentation](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)。For more, see the [MDN box-sizing documentation](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing).

### Last Flow

當使用float輸出時，可控制 row 裡面的 last 元素的浮動方向。
The float-direction for the last element in a row when using the float output.

** 設定 **

關鍵字:	```last-flow```

範圍:	global

選項:	```from``` | ```to```

預設:	```to```

* ```from``` : 預設 layout 裡面的所有其他元素皆是```ltr``` (由左至右)來排列， float 方向會從左邊開始。而這個設定會讓 “last” 元素靠左，同時連同著其他元素。
This is the default for all other elements in a layout. In an ```ltr``` (left-to-right) flow, the from-direction is ```left```, and this setting would float “last” elements to the left, along with the other elements.
* ```to``` :在許多情況下(尤其是 ```fulid``` grids )，它可以幫助你控制 row 裡面的 last 元素的 float 方向設為相反的方向。
 In many cases (especially with ```fluid``` grids), it can be helpful to float the last element in a row in the opposite direction.


### Debug

Susy 有一些工具可以幫助你的 Layout 進行除錯. 這些設定能夠控制除錯環境。Susy has a few tools to help in debugging your layout as you work. These settings help you control the debugging environment.


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
> Grid images 並不精準，瀏覽器在 background images 上會有額外sub-pixel 的麻煩，這些只提供簡單的測試，像素上並非完美精準，使用 ```to``` 側在 grid image(```right```如果你的 flow 是```ltr```)，它將會減少幾個像素。 Grid images are not exact. Browsers have extra trouble with sub-pixel rounding on background images. These are meant for rough debugging, not for pixel-perfect measurements. Expect the ```to``` side of your grid image (```right``` if your flow is ```ltr```) to be off by several pixels.



### Debug Image

Toggle the available grid images on and off.  
切換 grid images 開啟或關閉
** 設定 **

關鍵字:	```debug image```

範圍:	global, local [container only]

選項:	```show``` | ```hide``` | ```show-columns``` | ```show-baseline```

預設:	```hide```

* ```show``` : 顯示 grid images ，會出現在容器元素的背景上來進行除錯，如果你使用 [Compass vertical rhythms](http://compass-style.org/reference/compass/typography/vertical_rhythm/) (或者設定你自己的 ```$base-line-height``` 變數， Susy 也會顯示 baseline grids )。Show grid images, usually on the background of container elements, for the purpose of debugging. If you are using [Compass vertical rhythms](http://compass-style.org/reference/compass/typography/vertical_rhythm/) (or have set your own ```$base-line-height``` variable) Susy will show baseline grids as well.
* ```hide``` :隱藏全部除錯用的 grid images。Hide all grid debugging images.
* ```show-columns``` : 只顯示橫向的 grid-columns ，即使 baseline grid 是存在的。Show only horizontal grid-columns, even if a baseline grid is available.
* ```show-baseline``` : 顯示 baseline grid，如果 ```$base-line-height``` 變數是存在的。Show only the baseline grid, if the ```$base-line-height``` variable is available.

### Debug Output

The debug image can be output either as a background on the container, or as a generated overlay.
讓除錯影像能夠輸出背景在 container 上，或客製在 overlay 上。 
** 設定 **

關鍵字:	```debug output```

範圍:	global, local [container only]

選項:	```background``` | ```overlay```

預設:	```background```

* ```background``` : 除錯影像會產生在 container 元素的背景上。Debugging images will be generated on on the background of the container element.
* ```overlay``` : 除錯影響會產生在 container 的 ```::before``` 元素上，在預設情況下， overlay 會隱藏起來，但會產生[切換]功能在角落的可視區域上，當滑鼠滑曳過去時便會顯示。 Debugging images will be generated as an overlay using the container’s ```::before``` element. By default, the overlay is hidden, but we also generate a [toggle](http://susydocs.oddbird.net/en/latest/settings/#settings-debug-toggle) in a corner of the viewport. Hover over the toggle to make the overlay appear.

### Debug Toggle
如果你使用 grid overlay 的選項， Susy 會產生一個可以切換顯示/關閉的功能在 overlay 上。當滑鼠滑過去便顯示。你可以切換在任何角落於可視區域上，其中包含```top```、```right```、```bottom```、```left```。If you are using the grid overlay option, Susy will generate a toggle to show/hide the overlay. Hovering over the toggle will show the overlay. You can place the toggle in any corner of the viewport using a combination of ```top```, ```right```, ```bottom```, and ```left```.


** 設定 **

關鍵字:	```debug toggle```

範圍:	global

選項:	```right``` | ```left and top``` | ```bottom```

預設:	```top right```

### Debug Color

產生 grid image 的 columns 顏色。Change the color of columns in the generated grid image.

** 設定 **

關鍵字:	debug color

範圍:	global

選項:	< color >

預設:	```rgba(#66f, .25)```

### Custom Support

Susy 提供了一些常見的 helpers 功能，你可以使用它們，或者使用第三方 libary ，像是 Compass 或 Bourbon。There are several common helpers that you can tell Susy to use, if you provide them yourself or through a third-party library like Compass or Bourbon.

### Custom Clearfix

告訴 Susy 使用全域清除浮動的 mixin。Tell Susy to use a global clearfix mixin.

** 設定 **

關鍵字:	```use-custom clearfix```

範圍:	global

選項:	< boolean >

預設:	```false```

* ```false``` : Susy 會使用 micro-clearfix 清除內部浮動的方法。Susy will use an internal micro-clearfix.
* ```true```: Susy 會尋找現有的 ```clearfix``` mixin，如果沒找到就會回傳 micro-clearfix 清除內部浮動的方法。Susy will look for an existing ```clearfix``` mixin, and fallback to the internal micro-clearfix if none is found.

### Custom Background Image

告訴 Susy 使用全域 background-image mixin，這僅用於在除錯上。Tell Susy to use a global background-image mixin. This is only used for debugging.

** 設定 **

關鍵字:	```use-custom background-image```

範圍:	global

選項:	< boolean >

預設:	```true```

* ```false``` : Susy 會直接輸出 background images 在CSS。Susy will output background-images directly to CSS.
*```true``` : Susy 會尋找現有的 ```background-image``` mixin(像是 Compass 或 Bourbon 其中一種)，如果沒找到就回傳普通的 CSS 輸出。Susy will look for an existing ```background-image``` mixin (like the ones provided by Compass and Bourbon), and fallback to plain CSS output if none is found.

### Custom Background Options
告訴Susy使用全域 ```background-size```, ```-origin```, and ```-clip``` mixins，這僅用於在除錯上。Tell Susy to use global ```background-size```, ```-origin```, and ```-clip``` mixins. This is only used for debugging.
** setting **

關鍵字:	```use-custom background-options```

範圍:	global

選項:	< boolean >

預設:	```false```

* ```false``` : Susy 會輸出 background-options 在 CSS 上。Susy will output background-options directly to CSS.
*```true``` : Susy 會尋找現有的 ```background-size```,  ```-origin```,以及 ```-clip``` mixins，(像是 Compass 或 Bourbon 其中一種)，如果沒找到就回傳普通的 CSS 輸出。Susy will look for existing ```background-size```, ```-origin```, and ```-clip``` mixins (like the ones provided by Compass and Bourbon), and fallback to plain CSS output if none is found.

### Custom Breakpoint Options

告訴 Susy 使用客製 breakpoint mixin，像是提供一個斷點 plugin。Tell Susy to use a custom ```breakpoint``` mixin, like the one provided by the Breakpoint plugin.

** 設定 **

關鍵字:	```use-custom breakpoint```

範圍:	global

選項:	< boolean >

預設:	true

* ```false``` : Susy 會回傳一個內部的 media-queries。 Susy will use an internal fallback for media-queries.
* ```true``` : Susy 會尋找現有的 breakpoint mixin，像是 [Breakpoint](http://breakpoint-sass.com) plugin，如果沒找到就回傳內部支持的 media-query。Susy will look for existing an breakpoint mixin like the one provided by the [Breakpoint](http://breakpoint-sass.com) plugin, and fallback to internal media-query support if none is found.

### Custom Box Sizing

告訴Susy使用全域 ```box-sizing``` mixin 設定。Tell Susy to use a global ```box-sizing``` mixin.

** 設定 **

關鍵字:	```use-custom box-sizing```

範圍:	global

選項:	< boolean >

預設:	```true```

*```false``` : Susy 會輸出 ```box-sizing``` 官方語法，以及 ```-moz``` 、 ```-webkit``` 的前綴詞版本。Susy will output ```box-sizing``` official syntax, as well as ```-moz``` and ```-webkit``` prefixed versions.
*```true``` : Susy 會尋找現有的 ```box-sizing``` mixin(像是 Compass 或 Bourbon 其中一種)，如果沒找到就會回傳 mozilla 、 webkit 及官方語法。Susy will look for an existing ```box-sizing``` mixin (like the ones provided by Compass and Bourbon), and fallback to mozilla, webkit, and official syntax if none is found.

### Custom Rem

告訴 Susy 使用 Compass rem support 模組。Tell Susy to use the Compass rem support module.

** 設定 **

關鍵字:	```use-custom rem```

範圍:	global

選項:	< boolean >

預設:	```true```

* ```false``` : Susy 會輸出長度值在 CSS 上。Susy will output length values directly to CSS.
* ```true``` : Susy 會尋找現有的 ```rem``` mixin，然後檢查 Compass 的 ```$rhythm-unit``` 跟 ```$rem-with-px-fallback``` 設定，如果沒找到就回傳普通的 CSS 輸出。Susy will look for an existing ```rem``` mixin, and check the ```$rhythm-unit``` and ```$rem-with-px-fallback``` settings provided by Compass, or fallback to plain CSS output if they aren’t found.

### Location

引用一個特定欄的 grid 在 row edges, isolation, 或 asymmetrical layouts 上，Locations 關鍵字不需要 ```at``` 的標誌。Reference a specific column on the grid for row edges, isolation, or asymmetrical layouts. Locations keywords don’t require the ```at``` flag.

** 設定 **

關鍵字:	```location```

範圍:	local

選項:	```first/alpha``` | ```last/omega``` | ```< number >```

預設:	null

* ```first```, ```alpha``` : 定位在1。Set location to 1.
* ```last```, ```omega``` : 定位在最後一欄，任何前面的欄數包含相關的 span。Set the location to the final column, and any previous columns included by the relevant span.
* ```< number >``` : 設定在任何一個 column-index 介於1至columns總數。Set the location to any column-index between 1 and the total number of available columns.

### Box Sizing

設定新的盒模型在任何元素上。Set a new box model on any given element.
** setting **

關鍵字:	```box-sizing```

範圍:	local

選項:	```border-box``` | ```content-box```

預設:	```null```

* ```border-box``` :輸出 ```box-sizing``` CSS 並設置為 ```border-box``` 模型。 Output ```box-sizing``` CSS to set the ```border-box``` model.
*```content-box``` : 輸出 ```box-sizing``` CSS 並設置為 ```content-box``` 模型。Output ```box-sizing``` CSS to set the ```content-box``` model.

### Spread

調整有多少 gutters 包含在一個 column span 上。Adjust how many gutters are included in a column span.

** 設定 **

關鍵字:	```spread```

範圍:	local

選項:	```narrow``` | ```wide``` | ```wider```

預設:	various...

* ```narrow``` : 絕大多數情況下， column-spans 包括 columns 之間的 gutters，如果 span 是 ```3 narrow``` ，那寬度便涵蓋了3個 columns 以及2個內部 gutters。這是預設在大多數情況下。 In most cases, column-spans include the gutters between columns. A span of ```3 narrow``` covers the width of 3 columns, as well as 2 internal gutters. This is the default in most cases.
* ```wide``` : 有些時候你需要包含1個外側 gutter 在一個 span 寬度，如果 span 是 ```3 narrow```，那寬度便涵蓋了3個 columns 與3個 gutters (兩個內部跟一個外側)。這是預設的幾個 margin/padding mixins。Sometimes you need to include one side gutter in a span width. A span of ```3 wide``` covers the width of 3 columns, and 3 gutters (2 internal, and 1 side). This is the default for several margin/padding mixins.
*```wider``` : 有些時候你需要包含兩邊外側的 gutters 在一個 span 寬度，如果 span 是 ```3 wider``` ，那寬度便涵蓋了3個 columns 與4個 gutters (2個內部跟2邊外側)。Sometimes you need to include both side gutters in a span width. A span of ```3 wider``` covers the width of 3 columns, and 4 gutters (2 internal, and 2 sides).

### Gutter Override

設定 gutter 寬度在元素上，或完全移除 gutter。Set an explicit one-off gutter-width on an element, or remove its gutters entirely.

** 設定 **

關鍵字:	```gutter-override```

範圍:	local

選項:	```no-gutters/no-gutter``` | ```< length >```

預設:	```null```

* ```no-gutters```, ```no-gutter``` : 移除所有 gutter 輸出。Remove all gutter output.
* ```< length >``` : 覆蓋計算出來的 gutter 並輸出明確的寬度。Override the calculated gutter output with an explicit width.

### Role

標示一個 grid 元素在嵌套的子元素上。Mark a grid element as a nesting context for child elements.

** 設定 **

關鍵字:	```role```

範圍:	local

選項:	```nest```

預設:	```null```

* ```nest``` : 標示一個內部 grid 元素在一個嵌套的 grids 上面。Mark an internal grid element as a context for nested grids.

> Note
注意
> This can be used with any grid type, but it is required for nesting with ```split```, ```inside```, or ```inside-static``` gutters.
> 這可以使用在任何一種 grid 種類，但是它需要嵌套在 ```split``` 、 ```inside``` 或 ```inside-static``` gutters。