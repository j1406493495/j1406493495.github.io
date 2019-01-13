---
layout: blog 
android: true 
istop: false
title: "LaunchMode和LaunchFlag" 
background-image: https://ws1.sinaimg.cn/large/006tNc79ly1fyyezydxpwj31510u0wis.jpg
date:  2019-01-07
category: Android
tags: 
- Android


---

## LaunchMode和LaunchFlag

### LaunchMode

- Standard

    标准模式，若已存在ABCD，再打开B，则会存在ABCDB。

- SingleTop

    若已存在ABCD，再打开B，则为ABCDB；若打开D，则为ABCD。

- SingleTask

    若已存在ABCD，再打开B，则为AB。只要Activity在一个栈中存在，那么多次启动此Activity都不会重新创建实例，且会destory掉它上面的Activity来保证整个栈中只有一个自己。

- SingleInstance

    单实例模式。若已存在ABC，再打开D，则会存在两个栈，分别为ABC和D；若此时D再打开C，则会存ABCC和D两个栈，ABCC点击返回键，存在ABC和D，此时界面显示C。

SingleTop和SingleTask模式，且目标Activity已在Task中，会调用onNewIntent()。

*注意：一个Task未结束时，按返回键不会跳到其他Task。*

![](https://ws2.sinaimg.cn/large/006tNc79ly1fyyexd1s26j30fy08pwff.jpg)

### LaunchFlag

假设当前存在ABCD：

- Intent.FLAG_ACTIVITY_NEW_TASK （默认）

    **打开B，会变成ABCDB。**该Flag为默认的Flag。

- FLAG_ACTIVITY_SINGLE_TOP

    **打开D，会变成ABCD。**相当于LaunchMode中的SingleTop。

- FLAG_ACTIVITY_CLEAR_TOP

    **打开B，会变成AB。**该Flag会清除B上面的Activity。

- FLAG_ACTIVITY_BROUGHT_TO_FRONT

    **打开B，会变成ABCD。再打开B，会变成ACDB。**再次打开B会将B带到前台。

- FLAG_ACTIVITY_REORDER_TO_FRONT

    **打开B，会直接变成ACDB。**会直接将B带到前台。

- FLAG_ACTIVITY_NO_HISTORY

    **打开带该Flag的E，再打开不带Flag的F，会变成ABCDF。**带有该Flag的Activity不会存放到Task中。

- FLAG_ACTIVITY_NO_USER_ACTION

    不响应onUserLeaveHint方法

