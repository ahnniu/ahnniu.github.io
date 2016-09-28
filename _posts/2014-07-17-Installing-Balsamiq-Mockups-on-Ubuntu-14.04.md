---
layout: post
title: Installing Balsamiq Mockups on Ubuntu 14.04
---


[Balsmiq Mockups](http://balsamiq.com/products/mockups/) 是一款手绘风格的快速UI原型工具，他内置了很多组件，有简单的交互功能（同一个路径下的链接切换）。最主要的是这个工具基本不需要学习，就是放到团队里面，不同分工的都可以无障碍的修改。可以使用Git等保存劳动成果，个人特别喜欢他的手绘风格。

先前一直是在Windows上使用，后来因为想学习一些Web开发切换到Ubuntu上，以下是在Ubuntu上的安装过程，走了一些弯路，给有需要的人一些参考。

![balsamiq mockups - Boogle](https://support.mybalsamiq.com/projects/examples/Boogle.png)
![balsamiq mockups - wiki](https://support.mybalsamiq.com/projects/examples/Wiki.png)

## Install GDebi

使用Ubuntu的软件中心安装 abdobeair会提示软件质量欠佳，最后也安装不成功，后续使用 GDebi来安装.deb包。

> gnome-keyring is required by AIR, gdebi is required to install the balsamiq package which will fail as “bad quality” in the Ubuntu Software Center. You can skip the gdebi install if you’re using X File Package (or probably anything other than Ubuntu Software Center) to install .deb packages.

```bash
$ sudo apt-get install gnome-keyring gdebi gdebi-core
```

## 下载 & 安装 adobeair

Adobe Air 已经不在维护Linux版本，我们获取最近的一个版本(2.6), 有一个.Deb包，我们采用这个 （也有.bin的，这个比较麻烦，而且不太容易成功）

下载地址：http://airdownload.adobe.com/air/lin/download/2.6/adobeair.deb 

下载后，在浏览文件找到，右键 -> 打开方式 -> GDebi Package Install 开始安装，安装过程中会让输入密码。

## 下载 & 安装 Balsamiq Mockups

> NOTE: 注意，下载32bit的，64bit可能会出问题。

到 http://balsamiq.com/download/ 官网上下载 Ubuntu 32bit：
目前版本地址为：http://builds.balsamiq.com/b/mockups-desktop/MockupsForDesktop32bit.deb?nocache=90546

下载后，在浏览文件找到，右键 -> 打开方式 -> GDebi Package Install 开始安装，安装过程中会让输入密码。

## 注册

请购买，或者到windows上，你懂得。。。


## 使用中的一些注意事项

Balsamiq也在打造一个开放/分享平台，大家会把一些 组件，或者自己的作品分享出来，供大家参考，下载，而且还有一个历史版本的功能。

[Mockups To Go](https://mockupstogo.mybalsamiq.com/projects)

## Refrence

- [Balsmiq官方-Linux安装帮忙](http://support.balsamiq.com/customer/portal/articles/99007-linux-installation-woes)
- [这一篇最有效果](http://www.ipreferjim.com/2014/04/install-balsamiq-ubuntu-14-04/)
- [如果安装abode air失败可以参考一下](http://www.tkalin.com/blog_posts/installing-adobe-air-and-elance-tracker-on-ubuntu-13-10-saucy-salamander-64-bit)

## 番外

安装Adobe Air正是折腾了很久，中间安装成功是成功了，但是运行 mockups的.deb包，就说依赖关系不满足，后来下载了一个 32bit的包，使用 GDebi 安装居然安装成功了，我猜想是不是不支持64的原因。

不管如何，终于折腾的可以用了，这个mockup实在是一个好东西。





