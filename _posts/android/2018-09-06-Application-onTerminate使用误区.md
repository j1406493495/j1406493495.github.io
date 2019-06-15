---
layout: blog 
android: true 
istop: false
title: "Application onTerminate使用误区" 
background-image: https://i.loli.net/2019/06/15/5d04bbfdebb1513929.jpg
date:  2018-09-06
category: Android 
tags: 
- Android 
---



## 起因

项目中有代码在Application中启动了一个Service，顺着这个思路，我就想在Application中监视应用退出，停止这个Service。

网上搜了一下Application的生命周期，有不少人提到onTerminate()方法，说会在应用退出时执行。

## 使用

既然大家都说好，那我就来试一试，毕竟大家好才是真的好。

but，这就是个坑。。。

我添加在该方法中的日志根本没有打印。

## 填坑

```java
    /**
     * This method is for use in emulated process environments.  It will
     * never be called on a production Android device, where processes are
     * removed by simply killing them; no user code (including this callback)
     * is executed when doing so.
     */
    @CallSuper
    public void onTerminate() {
    }
```

注释翻译过来就是说

> 该方法只有在仿真器环境下才会执行，对，就是这么逗你玩！！！











