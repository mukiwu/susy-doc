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
