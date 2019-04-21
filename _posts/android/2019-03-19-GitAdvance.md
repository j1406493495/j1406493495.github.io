---
layout: blog 
android: true 
istop: true
title: "git 高级功能" 
background-image: https://ws4.sinaimg.cn/large/006tKfTcly1g0wu7euc67j31900u04qp.jpg
date:  2019-03-19
category: Android
tags: 
- Android


---

## Git 高级应用

为了演示效果，我先创建了一个 `Git` 仓库，名为 `gitWork`。

下图为该仓库的当前提交状态及分支情况：

- 项目展示

  ![](https://ws2.sinaimg.cn/large/006tKfTcly1g1hkacayvaj311002uq3b.jpg)

- 分支展示

  ![分支](https://ws4.sinaimg.cn/large/006tKfTcly1g1hkas7zo1j311m034q2z.jpg)

- `master` 分支展示

  ![](https://ws1.sinaimg.cn/large/006tKfTcly1g1hkbrm5hcj319k0lkwi9.jpg)

- `dev` 分支展示

  ![](https://ws1.sinaimg.cn/large/006tKfTcly1g1hkbrm5hcj319k0lkwi9.jpg)

- 项目图展示(虚线为 `git rebase master` 后的结果)

  ![](https://ws3.sinaimg.cn/large/006tKfTcly1g1hlctl9f0j317c0h4q4h.jpg)

**下面所有的讲述均已此仓库为实例。**

### git rebase

`git rebase` 命令执行结果（文件内容）和 `git merge` 一致，但是过程存在一些差别。

在 `dev` 分支中执行 `git rebase master`，表示以 `master` 分支为 `base`，将 `dev` 分支中的与 `master` 分支交汇节点（58381b）之后的所有节点**一个一个的**放到 `master` 分支中。

*效果参考上述项目展示图。*

**值得注意的是：** `rebase` 并不是直接将节点移动，而是在 `master` 分支创建了另外的两个节点，这两个节点与 `dev` 上的两个节点内容一致（废话，不一致的话，谁动了我的代码！！！）。

同 `merge` 一样，`rebase` 过程发生冲突的话，就请各位大佬自行解决一下。

### git bisect

假设在节点 f1cdc8 中埋了个 bug，直到节点 c68c4c 才暴露出来，为了揪出节点 f1cdc8，`git bisect` 就派上用场了。

- 第一步：我确认节点 58381b 是没问题的，记为 good；节点 c68c4c 是有问题的，记为 bad。

- 第二步：`git bisect start c68c4c 58381b` 开始查找问题节点。

- 第三步：二分法查找，标记跳动节点是 bad or good。

- 第四步：找到问题节点 f1cdc8。

- 第五步：`git bisect reset` 退出流程。

  ![](https://ws2.sinaimg.cn/large/006tKfTcly1g1is6acesoj31b60io7bg.jpg)

### git blame

当我们阅读团队中别人的代码时，有时候会对某一块代码疑惑不解，但是我不知道这是团队中哪位大佬写的啊，总不能一个一个问吧？

![](https://ws4.sinaimg.cn/large/006tKfTcly1g1isqu4zb3j30ws01u74h.jpg)

例如我想知道 test.txt 文件中的 "say master hello" 是谁写的？

执行 `git blame test.txt`，就可以看到如下信息了：

![](https://ws1.sinaimg.cn/large/006tKfTcly1g1isr5707yj30ys020aa9.jpg)

### git reflog

讲 `git reflog` 之前，先介绍一下 `git` 中的 `HEAD` 和 `branch`。**HEAD 和 branch 可以类同理解为 C 语言中的指针。** 

`branch` 指针指向某个节点，`HEAD` 既可以指向 `branch`，也可以指向节点。

`git checkout` 命令负责切换 `HEAD` 指针的指向。

接下来讲 `git reflog` 指令就会好理解很多，该指令会显示 `HEAD` 指针的所有走向记录，包含那些已经不需要的节点或分支记录。

`git reflog dev` 查看 `dev` 分支上的所有 `HEAD` 走向记录：

![](https://ws3.sinaimg.cn/large/006tKfTcly1g1it4eztv7j31ek0awtcp.jpg)










