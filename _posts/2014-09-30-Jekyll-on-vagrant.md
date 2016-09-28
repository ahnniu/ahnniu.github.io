---
layout: post
title: 在Vagrant上使用Jekyll
---

我的Github博客是基于 [chloerei/scribble](https://github.com/chloerei/scribble)的，这是一个Jekyll theme，以下是我在Vagrant上使用Jekyll的一些问题.

## Scribble Get started

1. Clone the repository to your computer.
    git clone https://github.com/username/scribble
2. bundle install
3. rake
4. go to http://localhost:4000 for preview.
5. Add posts in _posts_

## Problem with Vagrant

在vagrant环境下，我遇到两个问题：

### 问题1：

因为代码是在 Windows & Vagrant 共享的，起初我在Windows下Git clone, 在vagrant下执行 ```bundle install```时遇到各种问题，解决办法：

在 vagrant 下执行 git clone.


### 问题2：

执行 rake 失败，提示：
> Generating...   Liquid Exception: incompatible character encodings: ISO-8859-1 and UTF-8 in _posts/2013-10-11-placeholder-post.md
> 

解决办法：在执行rake前执行：

```sh
$ export LC_ALL=en_US.UTF-8
$ export LANG=en_US.UTF-8
$ rake
```

或者 添加到 /home/vagrant/.bash_profile中

```bash
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```

### 问题三

> Regenerating: 1 files at 2014-09-30 02:59:02   Conversion error: There was an error converting '_posts/2014-9-30-vagrant.md/#excerpt'.

在写Post时，提示转换失败，后来一点一点的定位问题，发现
    ```shell
没有被正确的识别为shell语法。

可能不能识别 shell这个语法？

Google到： [stackoverflow - Highlight bash/shell code in markdown](http://stackoverflow.com/questions/20303826/highlight-bash-shell-code-in-markdown)

其中，提到 [Defines all Languages known to GitHub](https://github.com/github/linguist/blob/master/lib/linguist/languages.yml)

我更换后就可以正常转换了。

<pre>
```bash
```
</pre>

## Reference

- [使用Jekyll时候遇到的问题](http://www.peter-lab.com/problems-when-using-jekyll/)
- [Incompatible character encodings bug with jekyll --server](https://github.com/snowplow/ansible-playbooks/issues/13)