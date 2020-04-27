---
layout: blog 
android: true 
istop: false
title: "git Greeit review 配置" 
background-image: https://i.loli.net/2020/04/27/lgZDQ3xfJihNXkW.png
date:  2020-04-27
category: Android
tags: 
- Android


---

使用`Gerrit`管理项目，能方便的添加`review`机制来审核代码。

但是每次`push`代码都需要跟上一大长串后缀，而且还需去`Greeit`平台手动添加审核者邮箱，实在是反人类。

好在通过配置`.git/config`文件可以方便的一步完成。

![](https://i.loli.net/2020/04/27/5rZdtVCFXY8gNHs.png)

如图所示，在`remote` 下添加高亮处代码即可完成配置，邮箱按需配置。

```
push = +refs/heads/*:refs/for/*%r=hrs@hzmct.com,r=admin@hzmct.com
```

配置完成后，`push`代码时只需执行`git push origin 分支名`即可，例如`git push origin dev`。

如果嫌这个还长的话，在`~/.bashrc`中配置`alias gpo='git push origin'`，保存并`source ~/.bashrc`生效。

最后使用`gpo dev`即可`push`代码，`Gerrit`会自动发邮件通知审核者。