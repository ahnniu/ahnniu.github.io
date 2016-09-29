---
layout:     post
title:      CSS Tips - 理解The CSS Box Model
date:       2016-08-09
summary:    CSS学习笔记 - 对CSS box model的理解
---

![the box](https://css-tricks.com/wp-content/csstricks-uploads/thebox.png)

| Width  | width + padding-left + padding-right + border-left + border-right  |
| Height | height + padding-top + padding-bottom + border-top + border-bottom |

## block / inline-block / inline

- block始终占据/充满一行，从上到下堆叠。
- inline-block可以设置width, hight, 并且在一行从左到右依次排列，直到碰到父元素的边界，wrap向下增长。
- inline是em,span等文字类标签，不可以显示的设置width, height值。从左到右排列，直到碰到父元素的边界，向下增长。也就是text wrap.

![inline-element](https://cdn.css-tricks.com/wp-content/uploads/2011/09/inline-element.png)
![inline-block](https://cdn.css-tricks.com/wp-content/uploads/2011/09/inline-block.png)
![block](https://cdn.css-tricks.com/wp-content/uploads/2011/09/block.png)

## width of box

width值的计算来源：
- 显示的指定width值
- 隐式的值，img有自己的width, 一个字符也有对应的宽度，子元素同样有指定的width,所有的这些，是content width.


### 没有明确指定width值时

有两种类型的模型计算width:
- block类型的
- inline-block
- inline. em等各种textarea标签

#### block box without no width

对于静态/相对布局的block，如果没有明确指定width值，此时：尽可能的延展到整个父元素的宽度。
`width + border + padding = 父元素的width`

#### inline-block

除了inline-block外，以下几种block盒子表现为inline-block:
- 绝对布局的block，但没有明确指定width值
- 浮动的block,但没有明确指定width指

> Note：绝对布局的block, 浮动的block一般需要设置width值，以避免产品不可预料的问题。

inline-block的表现是尽可能的收缩, 最大到同父元素的width.(The box is only as wide as it needs to be to accommodate the content, up to as wide as its parent element).

textarea类型的box, 他们不能指定宽度。特点是："text wrap". 随着文字的增加，box的width在增长，直到同父元素的宽度。然后text会换起一行，继续增长。

### 明确指定width值时

#### block level box

block level box同inline/inline-box不同的是堆叠的行为。

block level box不管width的值是多少，一行仅可以放一个block. 即使block的宽度离父元素的宽还有很远。(即使手动设置margin-right的值也没用)

inline/inline-box会在一行上从左到右依次摆放，若放不小，会向下增长（换一行）。

### width=百分比

首先百分比是相对父元素的。100%同父元素的width相同。此时，子元素width+padding+border可能会大于父元素。此时，要看父元素的overflow属性是怎么设置的。如果是默认的viable,则显示出来。

> 注： 如果子元素撑出父元素(x或y方向)，多出来的部分不会影响文档流。也就是说：多出来的部分不会向下挤压其他block, 那么可能出现层叠的情况，出现问题。

## height

同width类似，如果一个block没有指定height值，那么就是内部内容决定高度。

有一个比较容易困惑的是百分比的值。

- 如果父元素有确定的height值(多少px), 那么子元素使用百分比则是相对父元素的height,这比较容易理解。
- 如果顶级元素（指的是body下面的元素）height赋予了一个百分比，不管这个百分比是多少，他的高度都等于内部内容的高度。其子元素height设置为auto,也同样为内部内容决定。

参考： [CSS Almanac - height \| CSS-Tricks](https://css-tricks.com/almanac/properties/h/height/)

看这个例子：http://codepen.io/grayghostvisuals/pen/rJGBh

另外，min-height, max-height是用来覆盖height值的，其中，min-height的权重最高。

## line-height

line-height可以设置 inline / inline-block 的单行行高。

## box-sizing

规定了如何计算width的公式，提供了几种不同的算法。

## Reference

- [The CSS Box Model \| CSS-TRICKS](https://css-tricks.com/the-css-box-model/)
- [CSS Almanac - overflow \| CSS-Tricks](https://css-tricks.com/almanac/properties/o/overflow/)
- [CSS Almanac - display \| CSS-Tricks](https://css-tricks.com/almanac/properties/d/display/)