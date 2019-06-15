---
layout: blog 
android: true 
istop: false
title: "git commit 模版配置" 
background-image: https://i.loli.net/2019/06/15/5d04cd7353e8747207.jpg
date:  2019-02-26
category: Android
tags: 
- Android


---

1. 创建模版文件 CommitTemplate 到本地，例如 `/Users/woong/workSpace/configFiles/CommitTemplate`。

2. 执行配置命令

   > git config --global commit.template /Users/woong/workSpace/configFiles/CommitTemplate
   >
   > git config  --global core.editor vim  // 配置编辑器



### CommitTemplate 文件内容

> [Bug XXXX] 
>
> Cause: None
>
> Solution: None
>
> Fixed Version: None
>
> Branch: None

### 使用方式

​	终端输入 `git commit` 命令，无需添加 -m 参数，即会打开上面配置的编辑器进行 commit 信息编辑。

![](https://i.loli.net/2019/06/15/5d04cd8c74fbf75752.jpg)




