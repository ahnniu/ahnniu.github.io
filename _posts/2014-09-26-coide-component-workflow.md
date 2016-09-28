---
layout: post
title: CoIDE组件工作流
---

CooCox 正在加紧完成一个全新的IDE, 组件部分，也将会有一些大的修改，以下是CooCox要推广的组件式的开发方式 - 组件工作流。



## 本地组件库，Remote组件库

### 本地组件库

<pre>
Repos
├─paulniu
│  └─sd.git
├─ahnniu
│  ├─fatfs.git
│  ├─lcd1602.git
│  └─usb.git
├─ anonymous 
│  ├─ stm32.git
└─ ...
</pre>

- 本地组件库，是一堆git __bare__仓库，按照用户名，组件名二层目录组织
- 用户名需是唯一的，组件在用户名也需要是唯一的
- 本地组件库，没有权限管理，本地用户/电脑使用者（无论是否登录coocox）对本地组件库原则上拥有绝对的控制权
- 本地组件库的来源：他是Remote仓库的__不完整副本__，或者说是从Remote仓库中下载下来的

本地无登录/无网络 的情况，视为 匿名用户(anonymous)

### Remote组件库

- 同本地 组件库一样，也是一堆 git __bare__的仓库，按照用户名，组件名二层目录组织
- Remote库，有明确的用户认证，和权限管理系统
    + 权限：可以指定需要的人访问，读写 Remote仓库，无指定的人，是访问不了的
- 每个用户，在Remote端 都有自己的用户空间，这个空间，是他有绝对控制权限的
- Remote库，可以有 fork功能，相当于在远程复制一份别人的仓库(无写权限的)放到自己的空间内，还可以继续维护，升级，比如 paul/sd.git --> ahnniu/sd.git, ahnniu不可以操作paul的sd组件，但是可以操作 ahnniu/sd,git

## Git的工作流

### 集中式工作流

通常，集中式工作流程使用的都是单点协作模型。一个存放代码仓库的中心服务器，可以接受所有开发者提交的代码。所有的开发者都是普通的节点，作为中心集线器的消费者，平时的工作就是和中心仓库同步数据

![集中式工作流](http://git-scm.com/figures/18333fig0501-tn.png)

### 集成管理员工作流

由于 Git 允许使用多个远程仓库，开发者便可以建立自己的公共仓库，往里面写数据并共享给他人，而同时又可以从别人的仓库中提取他们的更新过来。这种情形通常都会有个代表着官方发布的项目仓库（blessed repository），开发者们由此仓库克隆出一个自己的公共仓库（developer public），然后将自己的提交推送上去，请求官方仓库的维护者拉取更新合并到主项目。维护者在自己的本地也有个克隆仓库（integration manager），他可以将你的公共仓库作为远程仓库添加进来，经过测试无误后合并到主干分支，然后再推送到官方仓库。

1. 项目维护者可以推送数据到公共仓库 blessed repository。
2. 贡献者克隆此仓库，修订或编写新代码。
3. 贡献者推送数据到自己的公共仓库 developer public。
4. 贡献者给维护者发送邮件，请求拉取自己的最新修订。
5. 维护者在自己本地的 integration manger 仓库中，将贡献者的仓库加为远程仓库，合并更新并做测试。
6. 维护者将合并后的更新推送到主仓库 blessed repository。

在 GitHub 网站上使用得最多的就是这种工作流。人们可以复制（fork 亦即克隆）某个项目到自己的列表中，成为自己的公共仓库。随后将自己的更新提交到这个仓库，所有人都可以看到你的每次更新。这么做最主要的优点在于，你可以按照自己的节奏继续工作，而不必等待维护者处理你提交的更新；而维护者也可以按照自己的节奏，任何时候都可以过来处理接纳你的贡献。

![集成管理员工作流](http://git-scm.com/figures/18333fig0502-tn.png)

## Git工作流在CooCox组件中的应用

- 集中工作流的场景
    + 我们是一个团队的，大家一起push代码
    + 我的一个组件，加入了一个很积极的贡献值，我很信任他，放权限，直接给他写
    + Wiki,大家都可以写
- 集成管理员工作流
    + 一个别人的组件，他有一些问题，但是他有不更新，没办法，我直接fork后更新并维护
    + 一个别人的组件，需求不是很满足，需定制为自己的
    + 在没有权限的时候，想贡献一些代码，这就是 pull request

## 组件/IDE应用规范

### 目录组织

以下是CoIDE应用类，组件类工程的目录组织样本

Application Project:

<pre>
C:.                                                      
└─CoIDE Projects                                         
    ├─Application Projects                               
    │  └─SD_MSC                                          
    │      │  README.md                                  
    │      │  SD_MSC.proj                                
    │      │                                             
    │      ├─App                                         
    │      │      App.c                                  
    │      │      App2.c                                 
    │      │                                             
    │      └─Components                                  
    │          ├─ahnniu                                  
    │          │  ├─SDCard                               
    │          │  └─stm32_cmsis                          
    │          ├─paulniu                                 
    │          │  └─FatFs                                
    │          └─tom                                     
    │              └─SDFileSys                           
    │                  │  README.md                      
    │                  │                                 
    │                  ├─HelloWorld                      
    │                  │  ├─HelloWorld2                  
    │                  │  └─SDFileSys_RW                 
    │                  │          SDFileSys_RW.c         
    │                  │          SDFileSys_RW.proj      
    │                  │                                 
    │                  ├─SourceCode                      
    │                  │      SDFileSys.c                
    │                  │      SDFileSys.h                
    │                  │                                 
    │                  └─Test                            
    │                      └─Suit0                       
    │                              SDFileSys_Test0.c     
    │                              SDFileSys_Test0.h     
    │                                                                

</pre>

Component Project

<pre>
C:.                                                      
└─CoIDE Projects                                         
    └─Component Projects                                        
        └─SDFileSys                                             
            │  SDFileSys.Comp.Proj                              
            │                                                   
            └─Components                                        
                ├─ahnniu                                        
                │  ├─SDCard                                     
                │  └─stm32_cmsis                                
                ├─new                                           
                │  └─SDFileSys                                  
                │      │  README.md                             
                │      │                                        
                │      ├─Doc                                    
                │      │  └─Reference                           
                │      │          image.jpg                     
                │      │                                        
                │      ├─HelloWorld                             
                │      │  ├─HelloWorld2                         
                │      │  └─SDFileSys_RW                        
                │      │          README.md                     
                │      │          SDFileSys_RW.c                
                │      │          SDFileSys_RW.proj             
                │      │                                        
                │      ├─SourceCode                             
                │      │      SDFileSys.c                       
                │      │      SDFileSys.h                       
                │      │                                        
                │      └─Test                                   
                │          └─Suit0                              
                │                  SDFileSys_Test0.c            
                │                  SDFileSys_Test0.h  
                |          |—— SDFileSys.Comp.Proj          
                │                                               
                └─paulniu                                       
                    └─FatFs                                     
</pre>


几个关键点：

CoIDE设置默认存储工程的路径，分为两类：

- 组件工程：
    + 新组件也存到Components目录下，只是user --> new
    + HelloWorld是一个个独立的工程，其使用的组件文件系统复用
    + 工程，组件，HelloWorld，都有README.md,而且，存在相应的根目录下

### 测试规范

### 测试框架

### 测试框架在不同平台上的移植

### 善用 Git Ignore

临时生成的文件，等等，要自动把其添加到忽略中


### 规范 & IDE协作，达到自动化/半自动化

只有达到半自动化，自动化，才可能规范成为事实，否则，很难要求大家按照一纸文书去做什么。

## 组件的生命周期

### 创建组件的起因

什么时候需要去创建组件？ 创建组件又影响了什么？他的结果/输出是什么？

- 动机
    + 当希望一个可复用代码，能够变成CoIDE的组件，在其他的一些工程中复用时
- 结果
    + 创建组件的结果，那就是 在组件列表中 多出来一个组件



### 创建一个组件工程

- 输入参数
    + 组件名
    + 路径名，用于保存工程在文件系统中

- 输出参数
    + 在指定的路径下，创建了一个git 仓库(非bare), 和一些必备的工程文件
    + 组件工程是由规范（包含目录组件等）的，IDE会按照规范生成

组件工程目录组织：

参加上面的目录规范。

### 创建组件的入口

- 从菜单，工具栏点击，目的很直接
- 在浏览组件库时，在某一个组件类下触发创建

### 组件撰写

撰写可能包含几个部分：

- 添加依赖
    + 我在撰写的，是一个高层次的组件，需要依赖一些底层，或者驱动组件
        * API调用
        * 编译通过，调试
- 自动添加测试组件，并生成测试frame代码
    + 后续逐步完善
- 撰写自己的可复用代码
- 撰写自己的文档，README.md
    + IDE自动生成README.md

特别注意，在添加依赖时：

- 添加依赖，仅仅是添加，不可修改依赖组件，否则依赖对象已经发生改变，依赖关系也已经不在成立
    + IDE添加依赖的时序
        1. 从本地组件库中 clone, checkout(master)组件到Components目录下，保持 用户名 / 组件的 结构
        2. 添加Src目录到工程的编译组里
        3. 添加必要的include

### 组件的编译，调试

组件工程，直接集成自动化测试，这一过程，可保障组件的质量

### Git Commit

当工程可以编译通过时，才可以commit

### Publish到本地

这一过程，主要是为了把这个组件，能够放到组件列表中，这样，后续其他的项目，可以直接通过导入的方式导入组件。

Publish的输入参数：

- 组件的owner
    + 当前登录用户名，如果没有登录，可提示用户登录，否则即将使用 anymous用户
- 组件的名称
    + 起初就被指定了，当用户可修改
- 组件的类别
    + 基础外设库
    + Driver
        * 如果是Driver，需提供入口，让其选择支持什么Driver芯片，注意：Driver芯片仅仅可选一个，这里的Driver芯片是一个统称，会在Driver芯片的文档中细化
    + 中间件

### Publish到服务器

当Publish到本地完成后，应该进一步提示：是否发布到CooCox上，他的几个好处：

- 云存储，云同步
- 多人可以协作
- ...


## 为组件添加Hello World

### 入口

在组件页，Hello World处，用户需指定Hello World的名称，注意：是否会与先前的Hello World 重名

### IDE的过程

hardware平台何时指定？

Step 1 Clone组件到一个用户目录，包含其所依赖的组件都clone下来，按照 Components的那个目录结构

Step 2 在组件的 Hello World目录下，创建一个工程

Step3 工程自动生成README.md, 和一个.c文件，有用户自己去编辑

Step4 编译，调试

Step 5 Git commit, 仅仅当编译通过才可以commit

Step 6 Push 到远端（分为 本地， 和服务器）

## 组件的修改

大概同组件的创建，只是有一点：

- 如果组件的作者 与登录的用户是一个，那么这个组件clone 的 位置不变副本仍然是：（Componnets/tom/sd）,如果作者与当前登录用户不一致，那么就clone到当前登录用户的空间，类似fork, Publish同样按照这个来操作（影响：在远端的那个位置创建）

## Hello World的修改

同创建，先clone, 在打开hello world工程即可，最后， commit, push, 仍然要检查 owner是否为当前用户




