---
layout: blog 
android: true 
istop: true
title: "git 高级功能" 
background-image: https://i.loli.net/2019/06/15/5d04cde52aaa283787.jpg
date:  2019-03-19
category: Android
tags: 
- Android


---

## Git 高级应用

为了演示效果，我先创建了一个 `Git` 仓库，名为 `gitWork`。

下图为该仓库的当前提交状态及分支情况：

- 项目展示

  ![](https://i.loli.net/2019/06/15/5d04cdf85cf4928813.jpg)

- 分支展示

  ![分支](https://i.loli.net/2019/06/15/5d04ce0e8fe3024174.jpg)

- `master` 分支展示

  ![](https://i.loli.net/2019/06/15/5d04ce1fd0de853257.jpg)

- `dev` 分支展示

  ![](https://i.loli.net/2019/06/15/5d04ce1fd0de853257.jpg)

- 项目图展示(虚线为 `git rebase master` 后的结果)

  ![](https://i.loli.net/2019/06/15/5d04cea368d6573140.jpg)

**下面所有的讲述均已此仓库为实例。**

### git rebase

`git rebase` 命令执行结果（文件内容）和 `git merge` 一致，但是过程存在一些差别。

在 `dev` 分支中执行 `git rebase master`，表示以 `master` 分支为 `base`，将 `dev` 分支中的与 `master` 分支交汇节点（58381b）之后的所有节点**一个一个的**放到 `master` 分支中。

*效果参考上述项目展示图。*

**值得注意的是：** `rebase` 并不是直接将节点移动，而是在 `master` 分支创建了另外的两个节点，这两个节点与 `dev` 上的两个节点内容一致（废话，不一致的话，谁动了我的代码！！！）。

同 `merge` 一样，`rebase` 过程发生冲突的话，就请各位大佬自行解决一下。

> 切记不要将有远程分支的本地分支（如 `origin/master->master`）`rebase` 到与其有分叉的其他分支，会导致重复提交。
>
> 因为 `rebase` 后的分叉节点 `commint hash` 和 远程 `commit hash` 不是同一个值。
>
> `rebase` 后 `git` 会要求你重新 `pull` 远程 `commit`，那么项目结构中就会存在 `rebase` 前和 `rebase` 后的重复分叉节点。

### git bisect

假设在节点 f1cdc8 中埋了个 bug，直到节点 c68c4c 才暴露出来，为了揪出节点 f1cdc8，`git bisect` 就派上用场了。

- 第一步：我确认节点 58381b 是没问题的，记为 good；节点 c68c4c 是有问题的，记为 bad。

- 第二步：`git bisect start c68c4c 58381b` 开始查找问题节点。

- 第三步：二分法查找，标记跳动节点是 bad or good。

- 第四步：找到问题节点 f1cdc8。

- 第五步：`git bisect reset` 退出流程。

  ![](https://i.loli.net/2019/06/15/5d04cec75cc0682415.jpg)

### git blame

当我们阅读团队中别人的代码时，有时候会对某一块代码疑惑不解，但是我不知道这是团队中哪位大佬写的啊，总不能一个一个问吧？

![](https://i.loli.net/2019/06/15/5d04cedfaef0317429.jpg)

例如我想知道 test.txt 文件中的 "say master hello" 是谁写的？

执行 `git blame test.txt`，就可以看到如下信息了：

![](https://i.loli.net/2019/06/15/5d04cef5e87ac42307.jpg)

### git reflog

讲 `git reflog` 之前，先介绍一下 `git` 中的 `HEAD` 和 `branch`。**HEAD 和 branch 可以类同理解为 C 语言中的指针。** 

`branch` 指针指向某个节点，`HEAD` 既可以指向 `branch`，也可以指向节点。

`git checkout` 命令负责切换 `HEAD` 指针的指向。

接下来讲 `git reflog` 指令就会好理解很多，该指令会显示 `HEAD` 指针的所有走向记录，包含那些已经不需要的节点或分支记录。

`git reflog dev` 查看 `dev` 分支上的所有 `HEAD` 走向记录：

![](https://i.loli.net/2019/06/15/5d04cf0a8cc5b64756.jpg)










