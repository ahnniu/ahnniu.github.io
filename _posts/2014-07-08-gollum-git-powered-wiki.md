---
layout: post
title: Gollum - Git Powered Wiki
---

## Description

[Gollum](https://github.com/gollum/gollum) is a simple wiki system built on top of Git. 

这是Github官方的开源项目，Github的Wiki应该就是基于Gollum的，最喜欢的是可以用Sublime Text + MarkdownEditing 写Wiki, 然后git commit, git push这个过程.

整个界面风格也是Github markdown的风格，简洁，清晰，大赞!


![Gollum](/images/post/2014-07-08-gollum.png)

## Install

> 注：安装到Linux上。

### 安装Ruby, Rails

安装RVM, 用于管理Ruby的安装

```bash
$ curl -L https://get.rvm.io | bash -s stable
$ source ~/.rvm/scripts/rvm
# 检查有没有安装成功
$ rvm -v
```

 用 RVM 安装 Ruby 环境

```bash
$ rvm install 2.1
$ rvm use 2.1 --default
# 检查有没有安装正确
$ ruby -v
$ gem -v
# Gem源替换为国内淘宝的
$ gem source -r https://rubygems.org/
$ gem source -a https://ruby.taobao.org
```

安装Rails

```bash
$ gem install rails
# 检查是否安装成功
$ rail -v
```

安装 gollum

```bash
$ gem install gollum
```

### 安装过程中的注意事项和问题

- 最好不选用root用户安装
- 因为GFW(网络防火墙)，可能安装会失败，如有条件翻墙先翻墙
- ruby使用时候，需要用 login模式，请先执行 ```bash bash --login ```

## Gollum使用

创建一个Git仓库

```bash
$ makedir wiki
$ cd wiki
$ git init
```

cd到刚创建的git repo路径，启动gollum服务

```bash
# 后台执行
$ gollum &
```

访问 http://192.168.33.10:4567/ 即可。

完。




