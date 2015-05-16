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

使用 Float 來排版的 layout 有時需要維護 rows 與 edges。

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