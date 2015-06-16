# Toolkit

SUSY 2.0的 toolkit 可以使用[縮寫語法](doc/shorthand.md) 來設定，使用縮寫可以掌控所有細節，以及迅速地調整你的預設值，所以你可以不止只綁定在一個 grid 或者一種輸出風格上。

The Susy 2.0 toolkit is built around our shorthand syntax. Use the shorthand to control every detail, and adjust your defaults on-the-fly, so you are never tied down to just one grid, or just one output style.

## Span [mixin]

設定 span 至任何元素在 layout 的一部分上，在 floated 或 isolated layout上, span會增加 floats, widths,以及 margins 設定。
Set any element to span a portion of your layout. For a floated or isolated layout, this will add necessary floats, widths, and margins.

** mixin **


格式 Format:	```span($span) { @content }```

$layout:	[*<span>*](doc/shorthand.md)

@content:	Sass content block

有許多方式可以使用 span mixin。

### Arbitrary Widths

最簡單的使用方式就是直接套入任何寬度在 mixin:
For the simplest use, pass any width directly to the mixin:

```
// arbitrary width
.item { @include span(25%); }

// float output (without gutters)
.item {
  float: left;
  width: 25%;
}
```

### Grid Widths

如果你使用的是一個 grid,你也可以使用 span columns 在 grid 上。
If you are using a grid, you can also span columns on the grid:

```
// grid span
.item { @include span(3); }

// output (7-column grid with 1/2 gutters after)
.item {
  float: left;
  width: 40%;
  margin-right: 5%;
}
```

### Row Edges

當你的 grid 是使用 before 或 after 的 gutters 設定時，有些情況你會需要標記一個 row 上的 first 或 last 元素，Susy 能夠幫你移除額外的 gutters:
When you use a grid with gutters before or after, you sometimes need to mark the first or last elements in a row, so Susy can remove the extra gutters:

```
// grid span
@include span(last 3);

// output (same 7-column grid)
.item {
  float: right;
  width: 40%;
  margin-right: 0;
}
```

由於舊版本的原因，使用 ```alpha``` 與 ```omega``` 可以代替 ```first``` 與 ```last```。

For legacy reasons, alpha and omega can be used in place of first and last.

### Context

Context是當你在任何時候要使用 fluid math, 與嵌套 grid 元素在其他元素內:
Context is required any time you are using fluid math, and nesting grid elements inside other elements:

```
// 10-column grid
.outer {
  @include span(5);
  .inner { @include span(2 of 5); }
}
```
當你使用 ```of``` 的標記時，裡面的 span 總數會等於你 of 設定的數值，在某些情況上，你可以設定 of 來改變嵌套元素裡面的 span 總數。
The of flag is used to signal context. The context is always equal to the grid-span of the parent. In some cases, you can imply changes in context by nesting elements inside the span tag itself:

```
// 10-column grid
.outer {
  // out here, the context is 10
  @include span(5) {
    // in here, the context is 5
    .inner { @include span(2); }
  }
}
```

### Nesting
如果Grids是使用 ```insides```, ```inside-static```, 或 ```split``` 的 gutters 設定不用擔心邊界的問題，但會遇到嵌套上的問題。  
如果裡面的子元素要對齊 grid 時，你可以使用```nest```：  
Grids with ```inside```, ```inside-static```, or ```split``` gutters don’t need to worry about the edge cases, but they do have to worry about nesting.  
If an element will have grid-aligned children, you should mark it as a nest:  

```
// inside, inside-static, or split gutters
.outer {
  @include span(5 nest);
  .inner { @include span(2 of 5); }
}
```

## Global Box Sizing


設定```box-sizing```在一個[global](http://www.paulirish.com/2012/box-sizing-border-box-ftw/) 選擇器上，同時搭配```global-box-sizing```設定。  

Set the ```box-sizing``` on a [global](http://www.paulirish.com/2012/box-sizing-border-box-ftw/) selector, and set the global-box-sizing to match.

** mixin **


格式 Format:	```global-box-sizing($box [, $inherit])```

Shortcut:	```border-box-sizing([$inherit])```

$box: ```	content-box | border-box```

$inherit:	```[optional] true | false```



你可以選擇參數來進行設定，例如將 ```$inherit``` 設定為 ```true``` 則仍會參考全域設定來繼承 ```box-sizing``` ，這個設定也能輕鬆設定一個組件的 ```box-sizing``` 參數來覆蓋全域，同時裡面的嵌套元素也會一併被修改。在預設設定下，當 ```$inherit```是 ```false``` 時，就只會更新該組件的 ```box-sizing```，裡面的嵌套元素就不會被影響。

 ```box-sizing```參數也可以作為縮寫語法的一部分，並用於 span mixin 上，這樣 Susy 便能夠設定元素們上的 ```box-sizing``` 。

Setting the optional argument, $inherit, to true will still globally set the box-sizing, but in a way such that a component can easily override the global box-sizing by setting its own box-sizing property. By setting box-sizing once on the component, all nested elements within the component will also be modified. The default behavior, where $inherit is false, would only update the box-sizing of the component itself. Nested elements are not affected when $inherit is false.

You can pass a box-sizing argument to the span mixin as part of the shorthand syntax, and Susy will set the element’s box-sizing to match.

```
// input
.item { @include span(25em border-box); }

// sample output (depending on settings)
.item {
  float: left;
  width: 25em;
  box-sizing: border-box;
}
```
強烈建議你使用 [global](http://www.paulirish.com/2012/box-sizing-border-box-ftw/) border-box 設定。特別是你有使用到任何種類的 inside gutter 。
We highly recommend using a [global](http://www.paulirish.com/2012/box-sizing-border-box-ftw/) border-box setting, especially if you are using inside gutters of any kind.

```
// the basics with default behavior:
* { box-sizing: border-box; }

// the basics with $inherit set to true:
html { box-sizing: border-box; }
* { box-sizing: inherit; }
```
Susy需要知道你選擇哪種盒模型來設計網頁，最好的辦法就是選擇下面其中一種縮寫設定。
Susy needs to know what box model you are using, so the best approach is to set global box sizing using one of Susy’s shortcuts.

```
// the flexible version:
@include global-box-sizing(border-box);

// the shortcut:
@include border-box-sizing;
```

如果你想手動修改全域 ```box-sizing```，或有準備其他 libray 來處理，請更新 ```global-box-sizing```設定讓 Susy 知道。

假使你需要支援 IE6/7 ，你可以使用 [polyfill](https://github.com/Schepp/box-sizing-polyfill) 來讓它正常運作。 

If you want to change the global box-sizing by hand, or it has already been changed by another library, update the global-box-sizing setting to let Susy know.

If you need to supprot IE6/7, there is a simple polyfill to make it work.

## Rows & Edges

使用 Float 來排版的 layout 在某些情境下需要調整 rows 與 edges 的設定。

Floated layouts sometimes require help maintaining rows and edges.

### Break

** mixin **


格式 Format:	```break()```

Reset:	```nobreak()```

Keywords:	```break | nobreak```

要新增一個 row 時，你需要清除前面所有的 floats。你可以使用關鍵字至 [span mixin](#) 裡來達成這個效果。當你需要應用 row-break 時，可以用 ```break``` mixin。

To create a new row, you need to clear all previous floats. This can usually be done using keywords with the span mixin. When you need to apply a row-break on it’s own, we have a break mixin.

```
.new-line { @include break; }
```

如果你需要覆蓋它，你可以使用 ```nobreak``` 來設為 ```clear:none```。
If you ever need to override that, you can use nobreak to set clear: none;.

```
.no-new-line { @include nobreak; }
```
```break``` 與 ```nobreak``` 都可以使用關鍵字至 ```span mixin``` 裡。

Both break and nobreak can also be used as keywords with the span mixin.

### First

** mixin **

格式 Format:	```first($context)```

Alternate:	```alpha($context)```

$context:	```<layout>```

> 注意 Note
>只適用於 gutter-position 設為 ```before```的時候。
>Only useful when gutter-position is set to before.

當 gutter-position 設為 ```before```時必須移除掉每個 row 裡面第一個元素的 gutter，可以使用關鍵字至 ```span mixin```裡，有時你會需要在 span mixin 外面 設定某個項目為 ```first```。

When gutter-position is set to before we need to remove the gutter from the first element in every row. This can often be solved using a keyword in the span mixin. Sometimes you need to set an item as first outside the span mixin.

```
.first { @include first; }
```

也可以使用 ```alpha``` mixin，它也具有相同的語法與輸出。
We also support an alpha mixin with the same syntax and output.

```first``` 與 ```alpha``` 這兩種都可以使用關鍵字至 ```span mixin``` 裡。

Both first and alpha can also be used as keywords with the span mixin.

### Last

** mixin **

格式 Format:	```last($context)```

Alternate:	```omega($context)```

$context:	```<layout>```

> 注意 Note
>只適用於 gutter-position 設為 ```after```的時候，但能夠解決 sub-pixel 四捨五入的問題。
>Only required when gutter-position is set to after, but can be useful in any context to help with sub-pixel rounding issues.

當 gutter-position 設為 ```before```時必須移除掉每個 row 裡面最後一個元素的 gutter，可以使用關鍵字至 ```span mixin```裡，有時你會需要在 span mixin 外面 設定某個項目為 ```last```。

When gutter-position is set to after we need to remove the gutter from the last element in every row, and optionally float in the opposite direction. This can often be solved using a keyword in the span mixin. Sometimes you need to set an item as last outside the span mixin.

```
.last { @include last; }
```

也可以使用 ```omega``` mixin，它也具有相同的語法與輸出。

We also support an omega mixin with the same syntax and output.

```last``` 與 ```omega``` 這兩種都可以使用關鍵字至 ```span mixin``` 裡。

Both last and omega can also be used as keywords with the span mixin.

### Full

** mixin **

格式 Format:	```full($context)```

$context:	```<layout>```

這一個縮寫效等同於 span(full)，讓創建出來的元素佔滿整個空間。

This is a shortcut for span(full), used to create elements that span their entire context.

```full``` 可以使用關鍵字至 ```span mixin``` 裡。

full can also be used as a keyword with the span mixin.

## Margins

使用縮寫 mixins 提供 左右邊的 margins。

Shortcut mixins for applying left/right margins.

### Pre 
** mixin **

格式 Format:	```pre($span)```

Alternate:	```push($span)```

$span:	```<span>```

依據設定的 flow 方向，在元素之前增加 margins 值。

Add margins before an element, depending on the flow direction.
```
.example1 { @include pre(25%); }
.example2 { @include push(2 of 7); }
```

### Post

** mixin **

格式 Format:	```pre($span)```

$span:	```<span>```

依據設定的 flow 方向，在元素之後增加 margins 值。

Add margins after an element, depending on the flow direction.

```
.example1 { @include post(25%); }
.example2 { @include post(2 of 7); }
```

### Pull

** mixin **

格式 Format:	```pre($span)```

$span:	```<span>```

依據設定的 flow 拉取相反的方向，在元素之前增加負值的 margins 。

Add negative margins before an element, pulling it against the direction of flow.

```
.example1 { @include pull(25%); }
.example2 { @include pull(2 of 7); }
```

### Squish

** mixin **

格式 Format:	```squish($pre [, $post])```

$span:	```<span>```

$post:	[optional] <span>

同時具有```pre``` 與 ```post```的功能應用在同個元素上。

Shortcut for adding both pre and post margins to the same element.

```
.example1 { @include squish(25%); }
.example2 { @include squish(1, 3); }
```

當需要同時使用 ```pre``` 與 ```post``` 時，你可以使用 ```squish``` 來節省時間，以避免還必須同時 ```pre``` 與 ```post``` 來達成效果。

When they share identical context, you can pass pre and post spans in the same argument. This is often the case, and saves you from repeating yourself.

```
// shared context
.shared {
  @include squish(1 3 of 12 no-gutters);
}
// distinct context
.distinct {
  @include squish(1 at 2, 3 at 6);
}
```
## Padding
左右側的 padding Mixin。
Shortcut mixins for applying left/right padding.

>注意
> padding Mixin 與容器寬度會根據你的盒模型來變動，瀏覽器預設的盒模型是 ```content-box```，寬度與 padding 會跟著累加，所以如果指定的元素含 ```span(3)``` 與 ```prefix(2)``` 的話，他的總寬度會變成5個 columns，建議使用 ```border-box``` 盒模型，使用 ```padding``` 就會變為向內剪掉寬度，所以指定的元素如果是 ```span(3)``` 就仍然會是 3 columns 。
>The interaction between padding and width changes depending on your given box-model. In the browser-default content-box model, width and padding are added together, so that an item with span(3) and prefix(2) will occupy a total of 5 columns. In the recommended border-box model, padding is subtracted from the width, so that an item with span(3) will always occupy 3 columns, no matter what padding is applied.

### Prefix
** mixin **
格式 Format:	```prefix($span)```

$span:	```<span>```

根據你 flow 的方向 增加 before 的 padding 值。

Add padding before an element, depending on the flow direction.
```
.example1 { @include prefix(25%); }
.example2 { @include prefix(2 of 7); }
```

### Suffix
** mixin **
格式 Format:	```suffix($span)```

$span:	```<span>```

根據你 flow 的方向 增加 after 的 padding 值。

Add padding before an element, depending on the flow direction.
```
.example1 { @include suffix(25%); }
.example2 { @include suffix(2 of 7); }
```

### Pad
** mixin **
格式 Format:	```suffix($span)```

$span:	```<span>```

$suffix:	```<span>```

使用 Pad 可同時使用 ```Prefix```、```Suffix``` 的功能。

```
// equal pre and post
.example1 { @include pad(25%); }

// distinct pre and post
.example2 { @include pad(1, 3); }
```
當需要同時使用 ```Prefix``` 與 ```Suffix``` 時，你可以使用 ```Pad``` 來節省時間，以避免還必須同時 ```Prefix``` 與 ```Suffix``` 來達成效果。

When they share identical context, you can pass pre and post spans in the same argument. This is often the case, and saves you from repeating yourself.

## Bleed
** mixin **
Format:	```bleed($bleed)```
$bleed:	```TRBL``` <span>

```Bleed```可以讓該元素出血於容器外，但並不會影響容器。同時也可以使用 ```span``` 的縮寫，可依序設定上、右、下、左的出血值在該元素上。

Apply negative margins and equal positive padding, so that element borders and backgrounds “bleed” outside of their containers, without the content be affected.

This uses the standard span shorthand, but takes anywhere from one to four widths, using the common TRBL pattern from CSS.

```
// input
.example1 { @include bleed(1em); }
.example2 { @include bleed(1em 2 20px 5% of 8 .25); }
// output
.example1 {
  margin: -1em;
  padding: 1em;
}
.example2 {
  margin-top: -1em;
  padding-top: 1em;
  margin-right: -22.5%;
  padding-right: 22.5%;
  margin-bottom: -20px;
  padding-bottom: 20px;
  margin-left: -5%;
  padding-left: 5%;
}
```
使用 ```bleed``` 的mixin時，使用 ```no-gutters``` 來覆蓋預設值便可保持 gutter 之間的整齊。
When possible, the bleed mixins will attempt to keep gutters intact. Use the no-gutters keyword to override that behavior.

### Bleed-x
** mixin **
Format:	```bleed-x($bleed)```
$bleed:	```LR <span>```

提供左側與右側的水平出血。

A shortcut for applying only left and right (horizontal) bleed.
```
// input
.example { @include bleed-x(1em 2em); }
// output
.example {
  margin-left: -1em;
  padding-left: 1em;
  margin-right: -2em;
  padding-right: 2em;
}
```

### Bleed-y
** mixin **
Format:	bleed-y($bleed)
$bleed:	TB <span>

提供上側與下側的垂直出血。
A shortcut for applying only top and bottom (vertical) bleed.

```
// input
.example { @include bleed-y(1em 2em); }
// output
.example {
  margin-top: -1em;
  padding-top: 1em;
  margin-bottom: -2em;
  padding-bottom: 2em;
}
```

## Isolate
**mixin**
Format:	```isolate($isolate)```
$isolate:	```<span>```

Isolation 的佈局方式是使用 float 來排版，主要是拿來解決 子像素跑版 的問題，Susy 提供全域的設定方式，或使用在 ```span``` mixin 縮寫以及單獨使用上。

```$isolate```參數採用制式的 ```span``` 縮寫，但任何長度或 grid-index

Isolation is a layout technique based on floats, but adjusted to address sub-pixel rounding issues. Susy supports it as a global output setting, or as a Shorthand keyword for the span mixin, or as a stand-alone mixin.

The $isolate argument takes a standard span shorthand, but any length or grid-index given is interpreted as an isolation location (unless location is otherwise specified with the at flag). The function returns a length value.

```
// input
.function {
  margin-left: isolate(2 of 7 .5 after);
}

// output
.function {
  margin-left: 15%;
}
```

mixin 會回傳所有屬性來設定 ```isolation```
And the mixin returns all the properties required for isolation.

```
// input
.mixin { @include isolate(25%); }

// output
.mixin {
  float: left;
  margin-left: 25%;
  margin-right: -100%;
}
```

## Gallery

** mixin **

Format:	```gallery($span, $selector)```
$span:	```<span>```
$selector:	```(nth-) child*``` | ```of-type```

```Gallery```提供畫廊式佈局的排版方式，並且提供 ```span```` 縮寫方式來 使用 ```nth-child``` 或 ```nth-of-type``` 控制佈局，並呈現在你 grid 上。

Gallery is a shortcut for creating gallery-style layouts, where a large number of elements are layed out on a consistent grid. We take the standard span shorthand and apply it to all the elements, using nth-child or nth-of-type selectors and the isolation technique to arrange them on the grid.

```
//每一張圖片在12 columns中會佔據 3個 columns
//所以會有四張圖片在同一行上
// each img will span 3 of 12 columns,
// with 4 images in each row:
.gallery img {
  @include gallery(3 of 12);
}
```

##Show Grid
**mixin**

Format:	```show-grid($grid)```
$grid:	```<layout>```

可以使用```show-grid``` mixin來顯示你的 ```container``` mixin的容器 grids，如果你需要提供到另外個grid上，也可以使用 ```show-grid``` mixin 套用到相同的 layout 縮寫參數上，除錯格線背景可使用 ```background``` 與 ```overlay```的格式輸出。

The easiest way to show you grids is by adding a keyword to your container mixin. If you need to apply the grid separately, the show-grid mixin takes exactly the same layout shorthand arguments, and can output the debugging grid image as either a background, or a triggered overlay.
```
body {
  @include container;
  @include show-grid(overlay);
}
```
> Warning 警告
> 這個功能並不準確，瀏覽器會有額外的子像素背景問題，這些僅提供比較粗糙的測試，所以並沒辦法提供完美的除錯，如果你的排版是 ```ltr```由左至右的話，將會少掉幾個像素。
>Grid images are not exact. Browsers have extra trouble with sub-pixel rounding on background images. These are meant for rough debugging, not for pixel-perfect measurements. Expect the to side of your grid image (right if your flow is ltr) to be off by several pixels.

## Breakpoint
Susy 有提供 media-query 的處理，以及支援 ```Breakpoint``` plugin，你需要安裝 ```Breakpoint```，並瀏覽他們的網站了解使用方式。
Susy has built-in media-query handling, and also supports integration with the Breakpoint plugin. To install Breakpoint, follow the instuctions on their site.

### Susy Breakpoint

**mixin**
Format:	```susy-breakpoint($query, $layout, $no-query)```
$query:	```media query shorthand (see susy-media)```
$layout:	```<layout>```
$no-query:	```<boolean>``` | ```<string> (see susy-media)```

susy-breakpoint() 在不同斷點上，能夠透過它來變更 layout的設定，不論是使用 ```susy-media```或第三方 Breakpoint plugin。

如果你要使用第三方 plugin，你可以瀏覽這兩篇文章：[Breakpoint: Basic Media Queries](https://github.com/at-import/breakpoint/wiki/Basic-Media-Queries)、[Breakpoint: No Query Fallbacks](https://github.com/at-import/breakpoint/wiki/No-Query-Fallbacks)

這個 mixin 可作為一個外部包裝嵌套到元素裡面，可以使用 susy functions 或 mixins 來 加入 media-queries、變更 layout 設定。

susy-breakpoint() acts as a shortcut for changing layout settings at different media-query breakpoints, using either susy-media or the third-party Breakpoint plugin.

If you are using the third-party plugin, see  and Breakpoint: No Query Fallbacks for details.

This mixin acts as a wrapper, adding media-queries and changing the layout settings for any susy functions or mixins that are nested inside.
```
@include susy-breakpoint(30em, 8) {
  // nested code uses an 8-column grid,
  // starting at a 30em min-width breakpoint...
  .example { @include span(3); }
}
```

### Susy Media

**mixin**
Format:	```susy-media($query, $no-query)```
$query:	```<min-width>[<max-width>]``` | ```<string>``` | ```<pair>``` | ```<map>```
$no-query:	```<boolean>``` | ```<string>```

susy-media mixin 提供基本的 media-query 的處理，以及內建 susy-breakpoint的功能

```$query```
一個單一個長度將使用在 min-width query上，兩個長度將會產生 min-、max-width 的 queries，一個屬性/值、map、以及一個獨立的字串也將會被直接使用。

The susy-media mixin provides basic media-query handling, and handles the built-in functionality for susy-breakpoint.

$query
A single length will be used as a min-width query, two lengths will become min- and max- width queries, a property-value pair, or map of pairs will become (property: value) queries, and a lonely string will be used directly.

```
// min
// ---
@include susy-media(30em) { /*...*/ }

@media (min-width: 30em) { /*...*/ }

// min/max pair
// ------------
@include susy-media(30em 60em) { /*...*/ }

@media (min-width: 30em) and (max-width: 60em) { /*...*/ }

// property/value pair
// -------------------
@include susy-media(min-height 30em) { /*...*/ }

@media (min-height: 30em) { /*...*/ }

// map
// ---
@include susy-media((
  min-height: 30em,
  orientation: landscape,
)) { /*...*/ }

@media (min-height: 30em) and (orientation: landscape) { /*...*/ }
```

```no-query```
如果設定為 ```true```，內容將不會有任何的 media-query，這時常會被用在創造一個獨立沒有 media-query 的 fallback。

如果使用在行內的class選擇器(例：.no-mqs)時，內容也將會同時輸出 media-query 以及裡面有設定到的選擇器。

這可以設定在全域的的 ```$susy-media-fallback```變數上。

如果你有設定 ```$susy-media```變數時，susy-media 也支持 自己命名的 media-queries。

true will render the contents to css without any media-query. This can be useful for creating separate no-query fallback files.

For inline fallbacks using a target class, pass in a string (e.g. .no-mqs) to use as your fallback selector. The contents will be output both inside a media-query and again inside the given selector.

This can be set globally with the $susy-media-fallback variable.

susy-media also supports named media-queries, which can be set using the $susy-media variable:

