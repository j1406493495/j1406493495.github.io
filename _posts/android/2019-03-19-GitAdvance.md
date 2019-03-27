---
layout: blog 
android: true 
istop: false
title: "git 高级功能" 
background-image: https://ws4.sinaimg.cn/large/006tKfTcly1g0wu7euc67j31900u04qp.jpg
date:  2019-03-19
category: Android
tags: 
- Android


---

## Git 高级应用

为了演示效果，我先创建了一个 Git 仓库，名为 gitWork。

下图为该仓库的当前提交状态及分支情况：

- 项目展示

  ![](https://ws2.sinaimg.cn/large/006tKfTcly1g1hkacayvaj311002uq3b.jpg)

- 分支展示

  ![分支](https://ws4.sinaimg.cn/large/006tKfTcly1g1hkas7zo1j311m034q2z.jpg)

- master 分支展示

  ![](https://ws1.sinaimg.cn/large/006tKfTcly1g1hkbrm5hcj319k0lkwi9.jpg)

- dev 分支展示

  ![](https://ws1.sinaimg.cn/large/006tKfTcly1g1hkbrm5hcj319k0lkwi9.jpg)

- 项目图展示(虚线为 git rebase master 后的结果)

  ![](https://ws3.sinaimg.cn/large/006tKfTcly1g1hlctl9f0j317c0h4q4h.jpg)

**下面所有的讲述均已此仓库为实例。**

### git rebase

git rebase 命令执行结果（文件内容）和 git merge 一致，但是过程存在一些差别。

在 dev 分支中执行 git rebase master，表示以 master 分支为 base，将 dev 分支中的与 master 分支交汇节点（58381b）之后的所有节点**一个一个的**放到 master 分支中。

*效果参考上述项目展示图。*

**值得注意的是：** rebase 并不是直接将节点移动，而是在 master 分支创建了另外的两个节点，这两个节点与 dev 上的两个节点内容一致（废话，不一致的话，谁动了我的代码！！！）。

同 merge 一样，rebase 过程发生冲突的话，就请各位大佬自行解决一下。

### git bisect

### git blame

### git reflog

## Git flow

### git flow init

### git flow feature start

### git flow feature finish








