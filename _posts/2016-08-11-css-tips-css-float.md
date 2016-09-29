---
layout:     post
title:      CSS Tips - 理解float
date:       2016-08-11
summary:    CSS学习笔记 - 对float的理解
---

## What is "Float"?

float的本意是解决 "text wrap"问题的，如下图：

![Print Layout 1](https://css-tricks.com/wp-content/csstricks-uploads/print-layout.png)

与绝对定位不同，浮动元素仍然是文档流的一部分。当浮动元素变化时，文档流会reflow. 会挤压旁边的其他元素。而绝对定位，可能会覆盖。见下图：

![Reflow 1](https://css-tricks.com/wp-content/csstricks-uploads/reflow-example-1.png)

![Reflow 2](https://css-tricks.com/wp-content/csstricks-uploads/reflow-example-2.png)

## 浮动的行为

### 行为一： block box -> inline box

浮动一个box, 把这个block变成了一个inline block，并且具备确切的宽度和高度。

宽度：
- 通过width显示指定
- 若没有显示指定，则是收缩到包裹内部内容的宽度

高度：
- 通过height, max-height, min-height显示指定的
- 若没有显示指定，则是收缩到包裹内部内容的高度

### 行为二： 向父元素的边框靠拢

float box首先会向上浮动，碰到已经render的元素边框或者父边框（它是第一个子元素）。
然后向指定的left,或right浮动，碰到父元素的left/right边框。

### 行为三： 下一个元素摆放

#### 下一个元素是inline / inline-block元素时

满足行内排放的规则（都是行内元素）

#### 下一个元素是block level元素时

下一个block 元素会忽略浮动元素的存在， 假装它不存在，占据整个一行（想象盒模型，margin-left, border, padding, 从最左边开始），但是内部的实际元素会绕开浮动元素的实体，不会使实体重叠。（比如说下一个block元素中是图片，图片会绕开浮动元素的实体，但是边框却在最左边。可通过调整block元素的margin实现两个边框相邻）

![next element render](https://css-tricks.com/wp-content/csstricks-uploads/correct-reflow.png)

### 行为四： 下下个元素与浮动无关， 满足盒模型的规则

inline按照inline的规则，block按照block的规则。（block始终占据一行，若上一个元素是inline, block元素会另起一行。若block元素下一个元素是行内元素，行内元素另起一行）

### 负值margin的影响

如果设置了负值的margin, 相当于给元素腾出了这么大的空间(比如两个float left元素，第2个元素设置了margin-left的负值)，本来这一行放不下的，设置负值margin就可能放在同一行，更有甚者，给人后面的元素跑到前面的错觉(margin-left: -100%)

当然，设置另外一个方向(float:left,设置margin-right的负值)，则相当于占用了一些空间。

### clear的行为

clear主要是改变浮动元素下一个block元素的摆放规则。

若没有clear, block会忽略浮动元素，假装浮动元素不存在，占据这一行（主要指的是盒模型），但实际会绕开实体元素，不会重叠。

若有clear, 那么这个元素就知道了浮动元素的存在，上面我们曾经说过，浮动元素会变成一个inline-block, 按照盒模型的堆叠，block元素会另起一行，进行render.

render后的结果，block会摆放在浮动元素的下方，自然父元素计算高度的时候，就像把浮动元素也包含了进行。


## 浮动的影响

### 对父元素的高度

如果浮动元素下一个元素没有clear, 那么会假装这些浮动元素不存在，高度自己会忽略它们。

如果浮动元素下一个元素有clear, 那么浮动元素就会影响下一个元素的render（x, y坐标）, 实际上相当于会把浮动元素包含进来一样。



## Reference

- [All About Floats \| CSS-Tricks](https://css-tricks.com/all-about-floats/)
- [CSS Almanac - float \| CSS-Tricks](https://css-tricks.com/almanac/properties/f/float/)
- [Why Containers Don’t Clear Themselves \| CSS-Tricks](https://css-tricks.com/containers-dont-clear-floats/)