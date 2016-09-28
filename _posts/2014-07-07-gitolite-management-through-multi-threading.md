---
layout: post
title: 多线程并发的Gitolite管理
---

新版的IDE将采用Git-backend的方式来管理组件，wiki等等，而git server， 我们采用了开源的gitolite(我在前一些[blog](http://ahnniu.github.io/2014/06/21/gitolite/)中写过如何搭建gitolite)。

Gitolite的初衷是解决小型团队的Git托管问题，很多操作，都需要管理员来（尽管可以有多个管理员，所有Git Server的管理员权限是一样的），比如创建一个远端仓库，管理一个仓库的权限，这些都需要管理员去操作 gitolite-admin这个仓库，普通用户是不能的。

这篇博文讲的是Gitolite 如何与 其他系统 很好的对接，让普通用户也具备一些功能，比如仓库的owner可以管理自有仓库的权限，可以自己创建仓库，可以创建team等等。

---------------------------


Git天生就是用来解决协作的，允许多人同一时间对同一文件做任意操作，而无需把文件作为公有资源，通过锁等等来达到同步的目的。

Gitolite的管理，又正好是基于Git的。他通过一个叫做gitolite-admin的仓库，来控制服务器上的用户管理，仓库管理。

## 背景

### 单线程操作不合适

Gitolite涉及了很多环节：

- 新用户注册
- Team的管理，新增，删除，添加人，删除人
- 仓库（组件，WIKI）的管理，新创建仓库，Fork, 删除，权限管理（加人，删人，修改权限）

如果由单一线程操作，而且还是文件操作，那么整个体验会很糟糕。

### Git 促成了多线程

原理即是Git分布式工作流程中的__集中式工作流__ , 看图：

集中式工作流程使用的都是单点协作模型。一个存放代码仓库的中心服务器，可以接受所有开发者提交的代码。所有的开发者都是普通的节点，作为中心集线器的消费者，平时的工作就是和中心仓库同步数据。

![集中式工作流](http://git-scm.com/figures/18333fig0501-tn.png)

__我们可以多创建一些节点，甚至这些节点可以不在同一台服务器上，那么我们以后可以通过增加节点，或者增加服务器来达到提升性能，满足并发性。__


## 单一线程操作流程

### 准备工作

![准备工作流程图](/images/post/2014-07-07-flowchart.png)

### 操作时序

![开始操作Gitolite](/images/post/2014-07-07-flowchart2.png)

## 多线程操作流程

首先有一个线程池，一个线程对应一个 gitolite-admin仓库的节点(副本)：

1. 等待并获取到资源
2. 开始按照单一线程的方式去写gitolite-admin
3. 如果失败，向上一层返回错误，成功则继续写入数据库，__（数据库是否会写失败？gitolite-admin需要与数据库保持数据一致性）__
4. Done.

## Refernce

- [ProGit - 分布式 Git - 分布式工作流程](http://git-scm.com/book/zh/%E5%88%86%E5%B8%83%E5%BC%8F-Git-%E5%88%86%E5%B8%83%E5%BC%8F%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B)
- [ProGit - 远程分支](http://git-scm.com/book/zh/Git-%E5%88%86%E6%94%AF-%E8%BF%9C%E7%A8%8B%E5%88%86%E6%94%AF)
- [Git merge 命令参考](https://www.kernel.org/pub/software/scm/git/docs/git-merge.html)
- [Git push 命令参考](https://www.kernel.org/pub/software/scm/git/docs/git-merge.html)