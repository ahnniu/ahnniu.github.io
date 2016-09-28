---
layout: post
title: 如何使得Git Log保持干净
---

相信大家日常的工作已经离不开git了, 无论是单位的项目,还是个人的知识管理, 无论是单位的电脑还是家里的电脑. 个人私有项目, 我存放在[Bitbucket](www.bitbucket.org), 比如个人Wiki啥的,公开的,或者学习性质的,我放在[Github](www.github.com)上.

日常经常遇到这样的场景:

- 马上下班了, 一个单元功能还没有完全做完, 下班回家了还想继续做,Git怎么提交?
- Git服务器上希望都是测试通过的版本, 但是这个功能需要1周才可以开发完,测试完.
    + 希望小伙伴们在服务器上看到的Git commit只有一次,并且是可用的
    + 我这一周来,也希望在不同的小节点/小里程碑做git commit, 以应对开发过程中回退等问题.
    + 怎么办?


## 方法一 正常commit, push (不推荐)

- 下班后,直接commit, 并push到服务器
- 回到家后pull, 编辑, commit, 在push

这种方式可以实现多终端的同步, 但是,不好的地方在于 Git log中, 有太多无用的commit和log信息. 尽管可以使用tag标记有用的版本, 但是,太多无用的log: 比如 "xx 完成一部分", "xx开发完毕,未测试"使得整个log信息混乱, 干扰伙伴们.

## 方法二 借助网盘(不推荐)

把整个local repo放到网盘中自动同步. 因为local repo中有太多生成/动态的东西, 不如一些rubygem. 产生很多无必要的同步. 另外, 编辑本地代码进行的太频繁, 网盘同步出发太多, 不推荐.

## 方法三 git reset, revert, commit --amend (不推荐)

Git提供了几种方式用来撤销commit, 但是前提是还没有push到远端服务器上, 如果已经push到远端服务器上, 若继续采取reset等操作, 使得远端服务器上的commit丢失,将给小伙伴们带来很大的麻烦.

## 方法四 类Pull Request流程 (推荐)

![Git集成管理者工作流](https://git-scm.com/book/en/v2/book/05-distributed-git/images/integration-manager.png)

Git中有一种工作流, 叫 集成管理者工作流, 类似Github Pull Request流程.

1. 公司 / 组织 有一个公共Git远端仓库 `company/repo.git` , 仅有少数人有push权限.
2. 个人开发者(person)在远端服务器上Fork 公司的公共repo, `person/repo.git`, 个人你对这个repo是有完全的控制权.
    + Github上直接fork即可
    + 如若不是使用的github, 那么先 在个人Git服务远端创建同名的repo, 然后把公司公共仓库clone到本地, 最后添加remote, push过去
3. 个人开发者可以通过pull `company/repo.git` 获取公共的更新, 然后push 到 `person/repo.git`, 保持最新
4. 个人开发者在本地repo中做修改, 测试完毕后, push到个人远端仓库`person/repo.git`, 然后告诉Leader,已经完成某某工作,仓库为`person/repo.git`, 分支为`person/issue_33`
5. Leader 获取(remote add, pull)他的更改, Review代码, 如若代码写的有问题,或不好,打回代码,个人开发者重复 步骤4. 如若代码OK, Leader在他的本地仓库 merge, 并push到公共远端.
6. 一次协作完成, 重复步骤3.

其中有一些地方需要说明的是:
- 个人开发者远端分支, 与公共远端分支, 肯定会有一个祖先commit, 但是, 两者的分支名称可以不同.
- 个人拥有对个人远端仓库的完全控制权, 而且, 其他伙伴也不依赖这个远端仓库,那么:
    + 个人可以任意想其远端push新的分支
    + 个人也可以任意的删除远端分支
    + 如若个人远端仓库已经很混乱, 并且确定已经不在需要,可重新fork公共repo.

### 如何保证集成管理者拉取的commit有一个干净的log   ?

原理就是混合使用Pull Request的工作流 和 Git reset命令. 如果是公司的项目, 那么可以在Github上面fork即可, 如果是个人的项目, 那么可以在创建一个副本repo(不推荐, github搞的有点乱, 但及时删除也可), 或者使用pCloud,Dropbox网盘, 在网盘中保存 bare repo.

假如公共远端repo为 `company/repo.git`, 个人远端为 `person/repo.git`, 公共远端有一个分支叫做'feature_xx', 下面是工作流:

1. Fork公共远端到 `person/repo.git`
2. 获取'company/feature_xx'分支, 然后push到 `person/feature_xx`
3. track `person/feature_xx`, 在本地创建本地分支 `local/feature_xx_tmp`
4. 在本地仓库中修改, 即使没有完成, 可随意commit, 后续可修改这部分commit.
5. 回到家中,获取`person/feature_xx_tmp`, 并继续在此分支上修改, 也无需太多关注commit的log信息.
6. 当在某一天,完成了这个功能,并测试通过, 基于`person/feature_xx_tmp`, 创建新的较为正式的本地分支,`local/feature_xx`, 并checkout.
7. `git reset HEAD~2 --mixed`, Reset到`person/feature_xx`处的commit, 最新的更改(位于workspace目录)保持最新不变, 但是中间的一些commit都不见了
8. `git commit -m "有意义的log"`, 并push 到远端. `git push person feature_xx`
9. 通知leader pull request请求, 若自己有权限, 可与 `company/feature_xx同步.
10. 一次与公共仓库的协作完成,重复下去. 个人仓库中的分支如 feature_xx_tmp保留, 后续如若需要,还可继续使用.

整个过程, 做的修改都在个人远端仓库, 不会影响别人.

我个人使用的是[pCloud](pcloud.com)的网盘, pCloud网盘蛮好用, 支持Linux, Mac, Windows, 可选择不同的同步目录, 另外一个杀手锏是可离线下载,推荐大家的使用. 后续考虑下是否写一篇pCloud的介绍.

