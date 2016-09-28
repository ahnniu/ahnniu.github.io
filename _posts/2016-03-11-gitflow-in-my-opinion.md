---
layout: post
title: Gitflow之我见 - 反驳 TW洞见《GITFLOW有害论》
---

昨天，在翻看自己Inoreader订阅的文章时，看到一个标题[《GITFLOW有害论》](http://insights.thoughtworkers.org/gitflow-consider-harmful/)，研读之下，发觉作者的观点和论据都太过牵强，甚至发觉作者对Git的应用和精髓都不熟悉，何来GITFLOW有害论？ 这是对先进思想的诋毁，遂发文以反驳之，以正观点，避免给更多人带来错误的指引。

这篇文章出自[TW洞见](http://insights.thoughtworkers.org/)，版权也归Thoughtworks公司所有。 本文作为一篇评论，仅是引用，本身也无意去侵权。

> 小插曲： 昨日晚6点多钟，正值下班的时间，看到这篇文章后，在下面做了评论，阐述了自己的观点，反驳了作者的一些论据，谁知今早居然被删除，表示无语。

下面进入正题，开始一句一句的解读，评论和反驳，仅仅是各抒己见，绝无诋毁作者之意，另，本人也仅仅是一名普通的软件开发者，对Git极其喜爱和推崇，发现有错误的观点，遂忍不住站出来要为Git说一句公道话。

文章通过一段一段的引用作者的原文章，然后跟上自己的评论，最后得出自己的观点。


## 开始解读，反驳


### 章节之 - 什么是Gitflow

> __引用原文：__

    ## 什么是Gitflow

    Gitflow是基于Git的强大分支能力所构建的一套软件开发工作流，最早由Vincent Driessen在2010年提出。最有名的大概是下面这张图。

    ![](http://insights.thoughtworkers.org/wp-content/uploads/2016/02/01-1.png)

    在Gitflow的模型里，软件开发活动基于不同的分支：

    The main branches
    -   master 该分支上的代码随时可以部署到生产环境
    develop 作为每日构建的集成分支，到达稳定状态时可以发布并merge回master
    Supporting branches
    -   Feature branches 每个新特性都在独立的feature branch上进行开发，并在开发结束后merge回develop
    Release branches 为每次发布准备的release candidate，在这个分支上只进行bug fix，并在完成后merge回master和develop
    Hotfix branches 用于快速修复，在修复完成后merge回master和develop

![](http://insights.thoughtworkers.org/wp-content/uploads/2016/02/01-1.png)

上面这些介绍，没什么可说的，仅有一点：

上面引用的那个图， 出自：[A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)， 我觉得应该像原作者标题，这仅仅是 `A successful Git branching model`，Gitflow这个词先前好像没有听过，是个自造词？ 

A successful Git branching model一文中，给出了大家如何更加规范的使用分支，我们可以用树的成长来比作软件开发：

- master是树干，是基本
- develop是大一点的枝干
- feature, bugfixes是一些小的子叶，长的好，可能会变成另外一个枝干，长的不好可能会终结，腐烂掉。

原作者主要体现的是，区分哪些是稳定的，主干的，哪些是异变的，是可能会出问题的。一切不稳定的东西，都不能影响大局。 这是全盘考虑，大局的结果。

如果整个都是一个枝干，那么如果出现严重问题，后果是比较严重的。

关于分支的理解，待下面再做详细解释。


有另外一个概念，叫：Workflow,  参见：[Distributed Git - Distributed Workflows](https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows), 中文版：[5.1 分布式 Git - 分布式工作流程](https://git-scm.com/book/zh/v1/%E5%88%86%E5%B8%83%E5%BC%8F-Git-%E5%88%86%E5%B8%83%E5%BC%8F%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B)，概念还是别搞混淆了好， 上述的工作流程中：给出了三种：

__集中式工作流__

集中式工作流程使用的都是单点协作模型。一个存放代码仓库的中心服务器，可以接受所有开发者提交的代码，这是类似svn的工作方式。
![](https://git-scm.com/figures/18333fig0501-tn.png)

__集成管理员工作流__

这个类似GitHub上开源项目的Fork和Pull Request，开源贡献者无中心仓库的push权限，但是可以把代码发到自己的仓库里，然后发Pull Request请求到开源项目管理者，Review代码，若合适，则考虑合并到中心仓库。

![](https://git-scm.com/figures/18333fig0502-tn.png)

__司令官与副官工作流__

这种模式，我认为是上述两种模式的综合。

![](https://git-scm.com/figures/18333fig0503-tn.png)

> Note: 这其实是《Pro Git》 这本书的章节，现已经合并到git官网，因V2中的图片有一个无法显示，中文版，我引用第一版。

----

> __引用原文：__

    Gitflow通过不同分支间的交互规划了一套软件开发、集成、部署的工作流。听起来很棒，迫不及待想试试了？等等，让我们先看看Gitflow不是什么。

    Gitflow不是Git社区的官方推荐工作流。是的，不要被名字骗到，这不是Linux内核开发的工作流也不是Git开发的工作流。这是最早由Web developer Vincent Driessen和他所在的组织采用并总结出的一套工作流程。

这里是个小批注： 根据上文，Gitflow是自造词, Linux内核开发的工作流， 作者又一次把工作流和分支模型概念搞混淆。 我们权当说树枝式的分支模型吧。

我们说下Linux社区吧，我觉得关于Linux，我还是有些发言权的。我司是做工控领域的嵌入式Linux, 虽不在那个团队，但也是有所见闻，并且咨询过的。
就说最近比较火的树莓派，不错， 树莓派每年出货量的一半，大概100W套，是我司生产的。 大家看看它的分支：

[Github - raspberrypi/linux](https://github.com/raspberrypi/linux/branches/all)
, 看看有多少个活跃分支，看看rpi-3.18.y这些分支是不是最终被合并到主干上。

> __引用原文：__

    Gitflow也不是Github所推荐的工作流。Github对Gitflow里的某些部分有不同看法，他们利用简化的分支模型和Pull Request构建了适合自己的工作流Github Flow。
    现在我要告诉你，Gitflow在企业软件开发中甚至不是一个最佳实践。ThoughtWorks Technology Radar在2011年7月刊，2015年1月刊里多次表明了Gitflow背后的feature branch模型在生产实践中的危害，又在最近一期2015年11月刊里专门将Gitflow列为不被推荐的技术。
    为什么Gitflow有问题

作者一会说分支模型，一会又说Pull Request工作流。 关于GitHub Pull Request，是对集中式工作流的简化，本质还是集中式工作流，只是系统帮着简化了一些操作，比如：

- Fork, 相当于首先clone到本地，然后在我的个人github空间创建一个同名的仓库，再push过去，而Github Fork点击下按钮就完成了
- Pull Request, 相当于个人开发者修复了一个bug或完成了一个功能，然后push到自己的远端仓库，然后告诉Leader，我做完了这个功能，放到了哪里，那个分支，您Review下看看有没有问题，没问题请合并下。


> __引用原文：__

    Gitflow对待分支的态度就像: Let’s create branches just because… we can!

    很多人吐槽吐槽，为什么开发一个新feature非得新开一个branch，而不是直接在develop上进行，难道就是为了……废弃掉未完成的feature时删除一个branch比较方便？

    很多人诟病Gitflow太复杂。将这么一套复杂的流程应用到团队中，不仅需要每个人都能正确地理解和选择正确的分支进行工作，还对整个团队的纪律性提出了很高的要求。毕竟规则越复杂，应用起来就越难。很多团队可能不得不借助额外的帮助脚本去应用这一套复杂的规则。

    然而最根本问题在于Gitflow背后的这一套feature branch模型。

    VCS里的branch本质上是一种代码隔离的技术。使用feature branch通常的做法是：当developer开始一个新feature，基于develop branch的最新代码建立一个独立branch，然后在该branch上完成feature的开发。开发不同feature上的developers因为工作在彼此隔离的branch上，相互之间的工作不会有影响，直到feature开发完成，将feature branch上的代码merge回develop branch。

    我们能看到feature branch最明显的两个好处是：

    各个feature之间的代码是隔离的，可以独立地开发、构建、测试；
    当feature的开发周期长于release周期时，可以避免未完成的feature进入生产环境。
    后面我们会看到，第一点所带来的伤害要大于其好处，第二点也可以通过其他的技术来实现。


作者的这一大段都在说分支，那么我说下我对分支的理解。

我们大家都知道，Git是分布式的，往大的方面说，其实分布式也可以理解为一个个中心仓库的分支，概念和branch极其的相似。就比如master分支，我们本身没有创建额外的分支，但是事实上存在了很多分支，比如：

- blessed_repo_remote/master
- blessed_repo_director_local/master
- develop1_remote/master
- develop1_local/master
- develop2...

再谈git branch的概念：

《Pro Git》中提到，git branch仅仅是一个指针，创建和销毁一个branch的开销极其的少，也极其的容易。

```bash
$ git checkout -b new_branch
```
一句话，就可以在checkout的时候新出来一个branch.

而认为__分支的精髓__在于：

- branch是一个个枝叶，他的生长变化不会影响到祖先枝干。
- 而且祖先枝干可以有选择的最终向哪个方向（branch）发展，主动权在祖先枝干。

如果扩散到中心仓库和分布式的开发者本地，远端仓库，也是这个道理。主动权在中心仓库，Fork的分支可以提供路线，但是不能左右中心仓库的发展。


作者说 `为什么开发一个新feature非得新开一个branch，而不是直接在develop上进行，难道就是为了……废弃掉未完成的feature时删除一个branch比较方便？`, 对分支枝干，枝叶的模型完全没有理解，就是不希望这个新的feature去影响主干，难道团队里面都是精英，一点差错都不会出？如果feature开发的过程中，发现一个原来的bug怎么办？ 新功能还没开发完，bug又急需解决？ 告诉我怎么处理？

即便还是使用develop分支，因为git是分布式的，如果有多个人开发，那么就说明有多少个develop分支的副本分支，只要不把完成一半的东西push到总仓库，也还好。 背后其实您已经再用分支了。


### 章节之 - merge is merge

> __引用原文：__

    ## merge is merge

    说到branch就不得不提起merge。merge代码总是痛苦和易错的。在软件开发的世界里，如果一件事很痛苦，那就频繁地去做它。比如集成很痛苦，那我们就nightly build或continuous integration，比如部署很痛苦，那我们就频繁发布或continuous deployment。 merge也是一样。所有的git教程和git工作流都会建议你频繁地从master pull代码，早做merge。

    然而feature branch这个实践本身阻碍了频繁的merge: 因为不同feature branch只能从master或develop分支pull代码，而在较长周期的开发完成后才被merge回master。也就是说相对不同的feature branch，develop上的代码永远是过时的。如果feature开发的平均时间是一个月，feature A所基于的代码可能在一个月前已经被feature B所修改掉了，这一个月来一直是基于错误的代码进行开发，而直到feature branch B被merge回develop才能获得反馈，到最后merge的成本是非常高的。

    现代的分布式版本控制系统在处理merge的能力上有很大的提升。大多数基于文本的冲突都能被git检测出来并自动处理，然而面对哪怕最基本的语义冲突上，git仍是束手无策。在同一个codebase里使用IDE进行rename是一件非常简单安全的事情。如果branch A对某函数进行了rename，于此同时另一个独立的branch仍然使用旧的函数名称进行大量调用，在两个branch进行合并时就会产生无法自动处理的冲突。

    如果连rename这么简单的重构都可能面临大量冲突，团队就会倾向于少做重构甚至不做重构。最后代码的质量只能是每况愈差逐渐腐烂。


关于分支的应用，正如上文所说，创建一个分支没什么开销，不管是命令，还是图形界面都很容易。同样，删除也是。

我们新开一个分支，把功能做好，测试确认没问题，马上就合并到枝干上，把这个分支给删除了。这个分支，甚至都不会出现在中心仓库的分支里，升值自己的remote仓库里，仅仅在个人本地仓库出现，可能一天，或者半天我们就完成了一个merge迭代，把这个分支给删除了。

作者的举例，也是有很多问题，请看：

- 关于feature开发的平均时间是一个月，合并到develop的例子，有几个地方有问题：
    + 为什么在开发feature的时候去修复bug? 到底是feature引发的bug还是原来的bug? 如果解决，会不会因为feature带来更多的问题？ ==》 根据树木成长的模型，bug我们要追本溯源，找到到底是哪里的问题，然后再哪里，新起一个分支解决，解决完，测试无问题，即可合并到主干。Feature的分支，可以随时获取develop分支，已获得更稳定的基础代码。
    + 同样，体现软件开发工程中耦合的问题。软件开发中，我们清楚减少耦合，git应用中，我们同样需要。
- 关于说merge冲突的例子：
    + 作者拿代码重构来举例，显然不是很合适。代码重构小的说是rename方法，大方面说是改接口，再往大的说是升级版本。简单说 1.0, 1.1都在不断迭代，然后作者说把 1.0, 1.1合并吧，这不搞笑吧。
    + 对冲突的理解，也有偏差。git merge什么时候会出现冲突：当出现两种截然不同的路子时候。比如原版本是A（向南走）, 你提交的是B(向东走)， 合并别人的是C(向北走)，向东，向北，是两种截然不同的方向，git当然不知道选择谁？ 解决冲突更多的是仲裁，相当于裁判员，这个是机器帮不了的，尽管是用svn,svn同样也会出现冲突，同样需要仲裁。


### 章节之 - 持续集成

> __引用原文：__

    持续集成

    如果feature branch要在feature开发完成才被merge回develop分支，那我们如何做持续集成呢？毕竟持续集成不是自己在本地把所有测试跑一遍，持续集成是把来自不同developer不同team的代码集成在一起，确保能构建成功通过所有的测试。按照持续集成的纪律，本地代码必须每日进行集成，我想大概有这几种方案：

    每个feature在一天内完成，然后集成回develop分支。这恐怕是不太可能的。况且如何每个feature如果能在一天内完成，我们为啥还专门开一个分支？
    每个分支有自己独立的持续集成环境，在分支内进行持续集成。然而为每个环境准备单独的持续集成环境是需要额外的硬件资源和虚拟化能力的，假设这点没有问题，不同分支间如果不进行集成，仍然不算真正意义上的持续集成，到最后的big bang conflict总是无法避免。
    每个分支有自己独立的持续集成环境，在分支内进行持续集成，同时每日将不同分支merge回develop分支进行集成。听起来很完美，不同分支间的代码也可以持续集成了。可发生了冲突、CI挂掉谁来修呢，也就是说我们还是得关心其他developer和其他团队的开发情况。不是说好了用feature branch就可以不管他们自己玩吗，那我们要feature branch还有什么用呢？
    所以你会发现，在坚持持续集成实践的情况下，feature branch是一件非常矛盾的事情。持续集成在鼓励更加频繁的代码集成和交互，让冲突越早解决越好。feature branch的代码隔离策略却在尽可能推迟代码的集成。延迟集成所带来的恶果在软件开发的历史上已经出现过很多次了，每个团队自己写自己的代码是挺high，到最后不同团队进行联调集成的时候就傻眼了，经常出现写两个月代码，花一个月时间集成的情况，质量还无法保证。


关于持续集成， 因为我是嵌入式行业的，对互联网中真正的开发，了解的不多，但是还可以类比一下，互联网一天一发布，我们一个月已发布，周期不同而已。

这里的问题核心在于计划和执行。今天要发布的，有哪些：
- 做哪些新功能，feature A, feature B
- 修复多少bug, bug1, bug2

然后开发者去做，当然，结果可能有的feature开发完了，有的没有，bug同样。如果基于上述树形的分支来去做一个个feature和bug, 最后集成的时候，反而更从容些，比如某些功能没做完，那咱就不合并了，如果仅仅基于一个分支做，我怀疑下班前应该发布不了的，为什么，功能没做完，最新的代码是有问题的代码。


### 章节之 - 如果不用gitflow

> __引用原文：__

    如果不用Gitflow…

    如果不用Gitflow，我们应该使用什么样的开发工作流？如果你还没听过Trunk Based Development，那你应该用起来了。


    是的，所有的开发工作都在同一个master分支上进行，同时利用Continuous Integration确保master上的代码随时都是production ready的。从master上拉出release分支进行release的追踪。

    可是feature branch可以确保没完成的feature不会进入到production呀。没关系，Feature Toggle技术也可以帮你做到这一点。如果系统有一项很大的修改，比如替换掉目前的ORM，如何采用这种策略呢？你可以试试Branch by Abstraction。我们这些策略来避免feature branch是因为本质上来说，feature branch是穷人版的模块化架构。当你的系统无法在部署时或运行时切换feature时，就只能依赖版本控制系统和手工merge了。

    Branch is not evil

    虽然long lived branch是一种不好的实践，但branch作为一种轻量级的代码隔离技术还是非常有价值的。比如在分布式版本控制系统里，我们不用再依赖某个中心服务器，可以进行独立的开发和commit。比如在一些探索性任务上，我们可以开启branch进行大胆的尝试。


作者又退回到了svn的流程，是好是坏咱不做评价。

> __引用原文：__

    技术用的对不对，还是要看上下文。

这句话说的好。


> __引用原文：__

    [参考文献]

    Long-lived-branches-with-gitflow in radar: https://www.thoughtworks.com/radar/techniques/long-lived-branches-with-gitflow
    Gitflow in radar: https://www.thoughtworks.com/radar/techniques/gitflow
    Feature Branching in radar: https://www.thoughtworks.com/radar/techniques/feature-branching
    Fowler on feature branch: http://martinfowler.com/bliki/FeatureBranch.html
    Fowler on continuous integration: http://www.martinfowler.com/articles/continuousIntegration.html
    Paul Hammant on TBD: http://paulhammant.com/2015/12/13/trunk-based-development-when-to-branch-for-release/
    Google’s Scaled Trunk Based Development: http://paulhammant.com/2013/05/06/googles-scaled-trunk-based-development/
    Trunk Based Development at Facebook: http://paulhammant.com/2013/03/04/facebook-tbd/
    Fowler on feature toggle: http://martinfowler.com/bliki/FeatureToggle.html
    Jez Humble on branch by abstraction:http://continuousdelivery.com/2011/05/make-large-scale-changes-incrementally-with-branch-by-abstraction/
    Fowler on branch by abstraction: http://martinfowler.com/bliki/BranchByAbstraction.html

没怎么看。


## 小结


[A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/) 提到的分支模型，从大局，从软件开发的生命周期出发的一种低耦合，可持续健康成长（类树木成长）的的模型，应该很科学才对。

另外，把分支模型，和Git的工作流混为一谈，也是不合理的，不管是分支，还是Fork, Pull Request的工作流， 都很科学。

本文只是自己的看法，和这些年来应用git的一些心得， 希望能给新的Git用户一个不错误的指引。 可能也有不对的地方，欢迎探讨。

另外，再次声明：绝无诋毁作者之意，也绝无侵权作者文章的意图，看到后，有不同观点，各抒己见。


