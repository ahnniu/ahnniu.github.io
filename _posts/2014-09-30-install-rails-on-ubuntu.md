---
layout: post
title: Ubuntu 上搭建Rails开发环境
---


## 安装RVM

> 注意，RVM是安装到一个用户的home下，一般不要使用root用户

```bash
$ curl -L https://get.rvm.io | bash -s stable
# 载入 RVM 环境
$ source ~/.bashrc
$ source ~/.bash_profile
```


```bash
# 检查RVM是否安装成功
$ rvm -v
```

终端->编辑菜单->配置文件首选项-> 标题和命令选型卡-> 勾选 “以登录shell方式运行命令”, 并重启 终端（或者 shell --login）

## 使用RVM管理Ruby版本
 
__列出已知的Ruby版本__

```bash
$ rvm list known
```

__安装Ruby, 可安装多个版本__

```bash
$ rvm install 2.1
$ ruby -v
$ gem -v
```


__使用一个Ruby版本__

```bash
# 设置使用的Ruby版本
$ rvm use 2.1
```

__设置默认版本__

```bash
$ rvm use 2.1 --default
```


__删除一个版本__

```bash
$ rvm remove 1.9.3
```


## Gemset

有时候不同的项目，可能要用到不同的Ruby版本，不同的gemset, RVM除了可以管理ruby版本外，还可以管理gemset.

gemset可以理解为是一个独立的虚拟gem环境，每一个gemset都是相互独立的。比如你有两个项目，一个是rails 2.3 一个是 rails 3.gemset可以帮你便捷的建立两套gem开发环境，并且方便的切换。

gemset是附加在ruby语言版本下面的，例如你用了1.9.2, 建立了一个叫rails3的gemset,当切换到1.8.7的时候，rails3这个gemset并不存在。

__建立gemset__

```bash
# 需首先指定此gemset依赖的ruby版本 (仅当切换到此ruby版本时，此gemset才可用)
$ rvm use 1.9.3
$ rvm gemset create rails32
# 列出有哪些gemset
$ rvm gemset list
```


__设定一个gemset为当前环境__

```bash
# use 可以用来切换语言，或者gemset,前提是他们已经被安装(或者建立)。并可以在list命令中看到。
$ rvm use 1.8.7
$ rvm use 1.8.7@rails23
```

__gemset的删除__

```bash
$ rvm use 1.8.7
$ rvm gemset list
$ rvm gemset delete rails23
```

__如果你想清空一个gemset的所有gem,想重新安装所有gem__

```bash
$ rvm gemset empty 1.8.7@rails23
```

__安装Rails环境__

首先选择一个要使用的ruby版本和gemset，然后安装指定的rails版本

```bash
$ rvm use 1.9.3@rails32
$ gem install rails -v 3.2.13
$ rails -v
```

## 卸载RVM

```bash
$ implode
```

## Refernce

- [Ruby China Wiki- 如何快速正确的安装 Ruby, Rails 运行环境](https://ruby-china.org/wiki/install_ruby_guide)
- [Ruby China Wiki - RVM Guide](https://ruby-china.org/wiki/rvm-guide)
- [如何从 0 开始学 ruby on rails （漫步版）](http://readful.com/post/12322300571/0-ruby-on-rails)
- [RVM'Document - gemset](http://rvm.io/gemsets)