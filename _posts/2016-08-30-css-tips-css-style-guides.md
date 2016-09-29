---
layout:     post
title:      CSS Tips - CSS 编程规范 & 架构
date:       2016-08-30
summary:    学习CSS规范和架构
---


可移植性，可扩展性，健壮可读性, 少即是多


## 架构

简单说就是模块化和命名，最大限度的重用。

张鑫旭在 [我是如何对网站CSS进行架构的](http://www.zhangxinxu.com/wordpress/2010/07/%E6%88%91%E6%98%AF%E5%A6%82%E4%BD%95%E5%AF%B9%E7%BD%91%E7%AB%99css%E8%BF%9B%E8%A1%8C%E6%9E%B6%E6%9E%84%E7%9A%84/) 一文中，对CSS分了下类

| ID |       模块       |                        简介                        |
|----|------------------|----------------------------------------------------|
|  1 | 简短的CSS reset  | 基本不需要                                         |
|  2 | CSS通用样式库    | 可以在任何网站中使用                               |
|  3 | 网站CSS样式库    | 特定网站通用样式，如常见color,margin,width,padding |
|  4 | 网站通用小图标   | 通用小图标样式                                     |
|  5 | 网站通用样式     | 独立元素的通用样式，如导航，菜单，按钮，选项卡控件 |
|  6 | 网站公共结构样式 | 最外层div, 分栏布局样式(margin单独页面设置)        |
|  7 | 单页面的精细结构 | 先从通用挑，剩下的单页面实现                       |

以下的展开从上文中摘录：

### CSS reset

能不用就不用

### CSS通用样式库

这部分算是可以积累下来的，可以用到多个网站中，拿来用即可。

```css
.l{float:left;}.r{float:right;}.cl{clear:both;}
.n{font-weight:normal; font-style:normal;}.b{font-weight:bold;}.i{font-style:italic;}
.fa{font-family:Arial;}.fg{font-family:Georgia;}.ft{font-family:Tahoma;}.fl{font-family:Lucida Console;}.fs{font-family:'宋体';}.fw{font-family:'微软雅黑';}
.tc{text-align:center;}.tr{text-align:right;}.tl{text-align:left;}
.tdl{text-decoration:underline;}.tdn,.tdn:hover,a.tdl:hover{text-decoration:none;}
.g0{color:#000000;}.g3{color:#333333;}.g6{color:#666666;}.g9{color:#999999;}.red{color:red;}.wh{color:white;}
.f0{font-size:0;}.f10{font-size:10px;}.f12{font-size:12px;}.f13{font-size:13px;}.f14{font-size:14px;}.f16{font-size:16px;}.f20{font-size:20px;}.f24{font-size:24px;}
.vm{vertical-align:middle;}.vtb{vertical-align:text-bottom;}.vt{vertical-align:top;}.vn{vertical-align:-2px;}.vimg{margin-bottom:-3px;}
.m0{margin:0;}.ml1{margin-left:1px;}.ml2{margin-left:2px;}.ml5{margin-left:5px;}.ml10{margin-left:10px;}.ml20{margin-left:20px;}.mr1{margin-right:1;}.mr2{margin-right:2px;}.mr5{margin-right:5px;}.mr10{margin-right:10px;}.mr20{margin-right:20px;}.mt1{margin-top:1;}.mt2{margin-top:2px;}.mt5{margin-top:5px;}.mt10{margin-top:10px;}.mt20{margin-top:20px;}.mb1{margin-bottom:1px;}.mb2{margin-bottom:2px;}.mb5{margin-bottom:5px;}.mb10{margin-bottom:10px;}.mb20{margin-bottom:20px;}.ml-1{margin-left:-1px;}.mt-1{margin-top:-1px;}
.p1{padding:1px;}.pl1{padding-left:1px;}.pt1{padding-top:1px;}.pr1{padding-right:1px;}.pb1{padding-bottom:1px;}.p2{padding:2px;}.pl2{padding-left:2px;}.pt2{padding-top:2px;}.pr2{padding-right:2px;}.pb2{padding-bottom:2px;}.pl5{padding-left:5px;}.p5{padding:5px;}.pt5{padding-top:5px;}.pr5{padding-right:5px;}.pb5{padding-bottom:5px;}.p10{padding:10px;}.pl10{padding-left:10px;}.pt10{padding-top:10px;}.pr10{padding-right:10px;}.pb10{padding-bottom:10px;}.p20{padding:20px;}.pl20{padding-left:20px;}.pt20{padding-top:20px;}.pr20{padding-right:20px;}.pb20{padding-bottom:20px;}
.rel{position:relative;}.abs{position:absolute;}
.dn{display:none;}.db{display:block;}.dib{-moz-inline-stack:inline-block; display:inline-block;}.di{display:inline;}
.ovh{overflow:hidden;}.ovs{overflow:scroll;}.vh{visibility:hidden;}.vv{visibility:visible;}
.lh14{line-height:14px;}.lh16{line-height:16px;}.lh18{line-height:18px;}.lh20{line-height:20px;}.lh22{line-height:22px;}.lh24{line-height:24px;}
.fix{*zoom:1;}.fix:after,.fix:before{display:block; content:"clear"; height:0; clear:both; overflow:hidden; visibility:hidden;}.z{_zoom:1;}
```

可以看到，上面把常见的padding, margin, font, bg-color等等写到类里面

### 网站CSS样式库

- 网站常见颜色，尤其是链接色
- 网站常见背景色（用bg+颜色前2字母表示，例如.bgf7表示background:#f7f7f7;）
- 网站常见边框色，这里类似于CSS 通用库中的margin属性，需拆分，例如.bbdd表示border-bottom:1px solid #ddd;每人的命名习惯不一样，但是尽量简单为好，甚至您可以像Google一样，直接两个字母（大小写混搭）表示。另外，一定要告知设计师，边框取色不宜多，不能凭感觉，要有所牺牲，也就是如果之前使用了#cecece的边框色，后面的即使使用#d0d0d0更好看，请使用#cecece代替，这就是团队协作。
- 网站遗留的单margin属性，例如，某一div留白较大，有个单独的margin-top:35px的属性，ok，这个属性请放在网站CSS样式库中，以.mt35{margin-top:35px;}保留，以供之后类似布局或需要的地方使用
- 网站遗留的单padding属性，例如，双栏自适应布局时，无论是浮动自适应，还是绝对定位自适应，都需要使用padding-left值，此时的padding-left多单样式，可抽取出来，以网站样式库的形式存在。记住，是单属性，且不可从通用元素中抽取单独的padding值，否则是给自己挖火坑。
- 网站遗留的width属性，在流体布局思想下，宽度是有限的，是珍贵的，需好好利用
- 网站常用的一些height属性，指一些高度值，例如height:18px; height:20px; height:24px; height:50px; height:100px; height:200px;等。

记住一个原则：**网站通用的元素（按钮，导航，选项卡的）的样式千万不能分离作为网站的CSS样式库.**

### 网站通用样式

里的“网站通用样式”可以说与“网站通用样式库”最为对立的两部分。网站通用样式专指“独立元素”的通用样式，所谓“独立元素”指的是网站通用的导航，菜单，按钮，选项卡，文本框装饰，图片装饰，圆角处理等等。这些“独立元素”的样式千万不能对其进行分离并归入“网站通用样式库”中，否则，日后会给你留下无尽的痛苦的！

我几乎从不对按钮或是导航进行定宽，都是宽度自适应，这样，可以大大节约Sprite背景图片以及CSS代码的成本。以前多有探讨，这里不多说了。

网站通用样式的代码量在整个CSS文件中所占据的比重是相当大的，如果您的CSS文件中发现CSS通用样式只占整个CSS文件的一小部分，尤其网站项目较大时，那就需要引起警惕，可能最后的结果就是CSS文件超负荷，最后反而一团糟。

### 网站公共结构样式

所谓“网站的结构样式”主要指的是最外框div的样式，一般限制网站的宽度(960~990不等)，还有就是网站的分栏布局样式，这里的样式仅仅针对主体结构，例如left_part，或是right_part；还包括网站的头部的一些公用结构，底部的样式结构等。

我是强烈建议公共结构仅仅定宽定高，设置浮动属性，切不可在结构样式上添加margin或是padding属性，这会使网站的公共结构的重用性大大降低！

### 单页面的精细结构

如果上述11项您都架构的非常好，那么您在编写每个具体页面的时候，就会非常轻松，非常迅速。因为80%~90%的样式都可以从上面11项中直接拿来用（上述11项全部都是网站公用样式）。

对于中型大型网站，我们可能要花3~4天甚至更多的时间分析页面设计图，处理CSS Sprite，架构网站的CSS，这段时间不写任何页面，就是处理网站（可以说是）唯一的CSS文件。所谓“磨刀不误砍柴功”，站在整站的角度上去思考CSS是非常重要的，这可以让你避免迷路，有助于写出精简高效的样式代码。

当我们把1-11项都完成了，就开始着手写页面了，这时候，整个网站的页面基本上都在你脑中了，您在下手的时候就会清除：我现在做的是哪个页面，在整个网站中扮演着什么样的地位，这个页面的CSS对整个网站的CSS有什么影响，这里的样式该怎么处理（分离，合并还是独立）等。

一般而言，就我个人经验，每个页面，即使这个页面看上去很复杂，其代码开销也是非常小的。其新增的代码开销去处有三处：一是分离一些样式归入“网站CSS样式库”中；二是凡事使用的CSS Sprite的样式与其他样式进行合并；三就是一些精细的复杂的样式，这些就是CSS文件的架构的最后一部分“单页面的精细结构了”，何为单页面的精细结构，如下图的样式，就可以说是精细结构，需要独立出来新写样式（可适当分离，注意“适当”一词）：

## 布局思想

### 固定布局 vs 流体布局 / 自适应布局

|  固定布局  |       流体布局       |
|------------|----------------------|
| 布局精致   | 松松垮垮             |
| 开发成本低 | 难度大，要求高       |
|            | 宽屏的利用           |
|            | 使用寿命高(宽屏更新) |
|            | 模块化与重用性       |

- 自适应布局是一种宏观布局思想，从整体入手，不用担心几个像素会破坏布局
- 固定布局属于精确布局思想，讲究规则，像素级布局

实践中，我们把两种布局思想结合起来：主体固定，内部自适应
- 主体部分最外框使用固定宽度
- 内部元素自适应外部边框
- 当某些地方内部不好实现自适应时，大可以切换为固定布局

参考：
- [网页布局思想浅议-淘宝新版首页为实例](http://www.zhangxinxu.com/wordpress/2010/01/%E7%BD%91%E9%A1%B5%E5%B8%83%E5%B1%80%E6%80%9D%E6%83%B3%E6%B5%85%E8%AE%AE-%E6%B7%98%E5%AE%9D%E6%96%B0%E7%89%88%E9%A6%96%E9%A1%B5%E4%B8%BA%E5%AE%9E%E4%BE%8B/)

### 鑫三无准则 - 无宽度，无图片， 无浮动

[张鑫旭](http://www.zhangxinxu.com/)在博客中提到页面重构三无准则：
- 无宽度
- 无图片
- 无浮动

其中无宽度，简单点是推崇自适应流体布局，无图片指的是用CSS实现一些特效，而不是用图片，无浮动，则考虑到浮动布局会有很多后遗症，推荐用其他的布局方法。

参考：
- [CSS页面重构“鑫三无准则”之“无图片”准则](http://www.zhangxinxu.com/wordpress/2011/05/css%E9%A1%B5%E9%9D%A2%E9%87%8D%E6%9E%84%E9%91%AB%E4%B8%89%E6%97%A0%E5%87%86%E5%88%99%E4%B9%8B%E6%97%A0%E5%9B%BE%E7%89%87%E5%87%86%E5%88%99/)
- [页面重构“鑫三无准则” 之“无宽度”准则](http://www.zhangxinxu.com/wordpress/2010/10/%E9%A1%B5%E9%9D%A2%E9%87%8D%E6%9E%84%E2%80%9C%E9%91%AB%E4%B8%89%E6%97%A0%E5%87%86%E5%88%99%E2%80%9D-%E4%B9%8B%E2%80%9C%E6%97%A0%E5%AE%BD%E5%BA%A6%E2%80%9D%E5%87%86%E5%88%99/)

### 宽度分离

所谓“宽度分离”，就是CSS中的width属性不与影响宽度的border/padding(有时候包括margin)属性共存, 减少页面宏观布局与精细模块的耦合。

即不能出现以下的些组合：
```css
{width:200px; border:1px solid #ccc;}
```
或者是：
```css
{width:200px; padding:20px;}
```

简单点，仍然是宏观与微观的说法，两者分离开，互不影响，减少耦合。width是宏观布局，一些枝枝叶叶的margin,padding,不应该影响布局，反过来，微观如何做，与宏观上的布局无关。

这样做的好处：
- 不用去计算了，1个像素1个像素的对
- 重用了，与宏观布局无关，可以用到不同的场景
- 低耦合，维护方便了，布局调整，无需调整内部细节，内部细节调整也不影响布局

如何实现宽度分离：

多加一层div,外层控制width,参与布局，内层div调整padding,margin,border,调整细节。

比如下面例子的：box_in

```html
<div class="box">
   <div class="box_in">
        <strong>温馨提示</strong><br />
        团购成功后，消费凭证将发送到手机：<strong class="cr">132 0803 3621</strong>，凭短信去商家消费。
    </div>
</div>
```

```css
.box{width:470px;}
.box_in{padding:20px; border:1px solid #ddd;}
```

参考：
- [CSS流体(自适应)布局下宽度分离原则](http://www.zhangxinxu.com/wordpress/2011/02/css%E6%B5%81%E4%BD%93%E8%87%AA%E9%80%82%E5%BA%94%E5%B8%83%E5%B1%80%E4%B8%8B%E5%AE%BD%E5%BA%A6%E5%88%86%E7%A6%BB%E5%8E%9F%E5%88%99/)

### 分离 / 去耦合 把握好度

分离是为了更好的重用，但是有些地方需要把握好度。

对于模块化的组件，如导航，选项卡，菜单，文本框时，就不应该过于分离了。

举例：文本框，一个网站的文本框大概样式相同，不同的是width.

```css
.inset{
    height:16px;
    background-position:0 -220px;
    background-color:white;
    border:1px solid #D3D2D4;
    padding:3px 0 2px 2px;
}
```

.insert类，提取了所有这些文本框的共同的地方，张鑫旭叫做样式合并(.class1,.class2)
对于易变的地方，则：
```css
.txtw1{width:163px;}
.txtw2{width:297px;}
.txtw3{width:397px;}
.txtw4{width:710px;}
```

```html
宽度1：<input type="text"  class="inset txtw1" />
宽度2：<input type="text"  class="inset txtw2" />
宽度3：<input type="text"  class="inset txtw3" />
```

注意：这里是模块化的组件模块，而且width也是易变的属性，不建议分离为通用类，以下代码不好，考虑width如果变的话怎么办。
```css
.w163{width:163px;}
.w297{width:297px;}
.w397{width:397px;}
.w710{width:710px;}
```

参考 张鑫旭的文章：[CSS的样式合并与模块化](http://www.zhangxinxu.com/wordpress/2010/07/css%E7%9A%84%E6%A0%B7%E5%BC%8F%E5%90%88%E5%B9%B6%E4%B8%8E%E6%A8%A1%E5%9D%97%E5%8C%96/)

## 规范

### 命名

先前我个人比较推崇语义化，搞得CSS命名很长，而且，命名还比较困难，该怎么起名了？
各种前缀，也看起来比较凌乱。

`语义是对自己的束缚，越是语义强的，越没有重用`

css不比代码，类名实在太多了。

张鑫旭在[精简高效的CSS命名准则/方法](http://www.zhangxinxu.com/wordpress/2010/09/%E7%B2%BE%E7%AE%80%E9%AB%98%E6%95%88%E7%9A%84css%E5%91%BD%E5%90%8D%E5%87%86%E5%88%99%E6%96%B9%E6%B3%95/)一文中，提到 `面向属性的命名方法`：
```css
.tr{text-align:right;}
.pb8{padding-bottom:8px;}
```

另外，与命名精密结合的是分离，低耦合，让样式的重用最大化。

当然，如果网站本身的架构不是对每个侧栏内容进行模块化处理的话，说实话，这里标题的分离还是有点危险的。想想看，如果那天产品经理说底部padding值要改成10像素，啊哦，如果你的网站架构不合理，含这类标题的模块到处塞，会改到你急火攻心，吐血三升而亡的。所以，对于分离，我反复强调，__“千万不要对网站通用的元素进行分离”__。

所以，记住精简高效的CSS命名准则之一：
**对于网站非通用元素，如果样式简单(1~2个属性)，对其分离并使用面向属性的命名方法**。

#### 精简高效CSS命名之“三无原则”

此“三无原则”就是：
**无ID，无层级，无标签**
更存粹的使用类(有时也需适当的放开，权衡利弊)

CSS命名就应该最简单、最直接，直捣黄龙。没有HTML标签，没有层级，这些通通滚蛋，不要。为什么不要，有三大原因：
- 限制重用
- CSS文件大小
- 降低了渲染效率
    + CSS渲染元素和使用JavaScript获取页面元素那是完全不一样的. #test .test{}， ul.test{}，#test ul{} 以及.test{} 中 .test的渲染效率是最高的。参考：《高性能网站进阶指南》,以及：[翻译-不同CSS技术及其CSS性能](http://www.zhangxinxu.com/wordpress/2010/08/%E7%BF%BB%E8%AF%91-%E4%B8%8D%E5%90%8Ccss%E6%8A%80%E6%9C%AF%E5%8F%8A%E5%85%B6css%E6%80%A7%E8%83%BD/)

如果不使用层级，可能会出现冲突问题，那么适当的使用前缀。



## 参考

CSS-TRICKS也给出了一些大公司的CSS规范，参考：
- [CSS Style Guides](https://css-tricks.com/css-style-guides/)



