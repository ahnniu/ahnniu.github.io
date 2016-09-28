---
layout: post
title: 再谈线框图
---

在产品需求阶段, 画一些原型图/线框图是必要的.先前,我一直在用Balsamiq Mockups, 后来, 系统从Windows迁移到Ubuntu上, 还写了一篇[Blog - Installing Balsamiq Mockups on Ubuntu 14.04](http://ahnniu.github.io/2014/07/17/Installing-Balsamiq-Mockups-on-Ubuntu-14.04/) 讲述如何在ubuntu上安装. 现在系统切换到了elementary OS, Balsamiq Mockups 则通过wine的方式运行.

![Demo 1](https://support.mybalsamiq.com/projects/examples/Boogle.png)
![Demo 2](https://support.mybalsamiq.com/projects/examples/Wiki.png)

Balsmiq Mockup的特点,我认为:

- 有很多的控件,从文本框,按钮,下拉框,图标, 都不少, 而且自制控件也不难
- 手绘风格, 看着也蛮漂亮.

最近在看[Mackenzie Child](mackenziechild.me)做的[Rails视频教程 - Blog & Portfolio](https://mackenziechild.me/blog-portfolio/), 他用了Adobe Illustrator来做原型图,流程包括:

- User Flow
    + 用户流程,在页面之间怎么跳转
- Low Fidelity Wireframe
    + 在纸张上面手绘,而且刻意用比较粗的Mark笔, 主要体现layout, 不涉及细节, 可画出多种选项,最后选择喜欢的方案
- Mid Fidelity Wireframe
    + 用AI实现, 单色方案, 细化纸张上面的想法,但是不出现文字和颜色.
- Design the pages
    + 接下来就开始用Adobe Photoshop设计页面了,加入颜色, 图标, 文件, 对齐等
- Coding to HTML
- Rails Coding

看完Mackenzie Child的视频后, 有种耳目一新的感觉.User Flow -> Low Fidelity Wireframe -> Mid Fidelity Wireframe -> Coding to HTML -> Rails实现, 很清晰.

AI设计的线框图的特点:

- 矢量图, 这是最大的特点, 全局, 局部都可以照顾得到
- 美观, 真的, 如果给客户看了, 效果肯定比Balsmiq Mockups手绘要好上很多
- 如果积累了一定数量的控件, 画图也很多,即便没有现做也很快,比如一个按钮, List什么的
- AI流行

Mackenzie Child 为了方便大家更快的做出线框图, 他整理了一套 页面布局和元素的控件库: [Wireframe & Userflow UI Kit Bundle](https://mackenziechild.me/resource/wireframe-userflow-ui-kit-bundle/), 也不算贵, 个人License 49$. 做User flow的时候, 找相近的copy过来即可. 做页面框图的时候, 选择相近的layouts调整, 大大节约了大家的时间.
如果经济负担的起, 建议大家购买.

我了,因为对AI也不熟, 现在对于web开发处于学习阶段, 另外,也是穷屌丝一个, 就下载了他的[例子](http://macke.nz/123L3b183U24), 自己动手慢慢画.

看完这个视频, 就有想试一下的冲动. 现在用的系统是Linux, 马上装了一个Windows 7 的虚拟机,把AI给装上了. 

- 首先是AI的学习, 不到一天的时间把, 看了一个教程, [Learn Adobe Illustrator in 30 Days Crash Course – FREE](http://www.vectordiary.com/illustrator/learn-adobe-illustrator/).
- 找了一些免费的库
    + [ikons](http://ikons.piotrkwiatkowski.co.uk/), 图标
    + [ui-tiles](https://pixelbuddha.net/ui-tiles/), 是一个免费的wireframe kits, 东西不多, 后来用到的也比较少, 多少是自己画
    + [IcoMoon-Free](https://icomoon.io/), 一些免费图标,比较多
- 开始画自己的, 自己要做一个twitter的clone版来学习rails, 画起来,慢慢的速度也快起来了,随着后续控件的积累, 相信不比Balsamiq Mockups慢.

也推荐大家试试, 下面是自己这两天画的.

![userflow](/images/post/2016-03-03/twitter_clone_userflow.png)
![picture 1](/images/post/2016-03-03/twitter_clone_mid_fidelity_wireframes-01.png)
![picture 2](/images/post/2016-03-03/twitter_clone_mid_fidelity_wireframes-02.png)
![picture 3](/images/post/2016-03-03/twitter_clone_mid_fidelity_wireframes-03.png)
![picture 4](/images/post/2016-03-03/twitter_clone_mid_fidelity_wireframes-04.png)
![picture 5](/images/post/2016-03-03/twitter_clone_mid_fidelity_wireframes-05.png)





