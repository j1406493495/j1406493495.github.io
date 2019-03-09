---
layout: blog 
android: true 
istop: false
title: "ReadHub项目Kotlin版开发指南(一、环境搭建)" 
background-image: https://ws2.sinaimg.cn/large/006tNc79ly1fzdaksqgo0j31930u0n40.jpg
date:  2019-01-21
category: Android
tags: 
- Android


---

## ReadHub项目Kotlin版转换指南(一、环境搭建)

## ReadHub项目简介

[ReadHub](https://github.com/j1406493495/ReadHub) 是冯大辉[ReadHub资讯](https://readhub.me/)的第三方客户端，由官方提供 Api 支持，Woong 独立开发的 Android 客户端。

### Java版本

Java版本已在 Github 开源，项目地址：[https://github.com/j1406493495/ReadHub](https://github.com/j1406493495/ReadHub)。

提供资讯阅读、稍后读、截图分享等功能。

### Kotlin版本

自从 Google 宣布 Kotlin 作为 Android 的官方开发语言后，Kotlin 在国内的热度在不断提高，我们很有必要去了解 Kotlin。

相对于干巴巴的去阅读 Kotlin 语法，实战总是最快的掌握方式。

于是 Kotlin 版本的 ReadHub 项目就正式启动了。

下面开始最初始的步骤，环境搭建。

##  环境搭建

### 一、打开 AndroidStudio，创建项目。

![](https://ws4.sinaimg.cn/large/006tNc79ly1fze9qb34d2j316c0q441y.jpg)

### 二、填写信息

![](https://ws4.sinaimg.cn/large/006tNc79ly1fze9q9dwjnj31io0u0tem.jpg)

### 三、选择项目类型及初始状态

选择适配手机。

![](https://ws1.sinaimg.cn/large/006tNc79ly1fze9q79k8ij31480u00xb.jpg)

为了直观的给大家看到效果，这里选择 “Empty Activity”，在实际的 ReadHub 项目中，我会使用 “Add No Activity”。

![](https://ws4.sinaimg.cn/large/006tNc79ly1fze9q3iummj314a0u0gow.jpg)

### 四、项目页面预览

![](https://ws2.sinaimg.cn/large/006tNc79ly1fze9q1btqjj316i0u07as.jpg)

![](https://ws3.sinaimg.cn/large/006tNc79ly1fze9pzqw0gj30u00u6guw.jpg)

可以看到代码页面居然没有关键字高亮，这对于一个开发者来说，是不可接受的。

下面我们去添加一下 Plugin，提高代码页面的颜值。

### 五、Plugin添加

![](https://ws4.sinaimg.cn/large/006tNc79ly1fze9px321oj31ac0u00yq.jpg)

### 六、HelloWorld

终于到最后一步了，点击页面上方的**绿色三角Run**，即可运行项目。

![](https://ws1.sinaimg.cn/large/006tNc79ly1fze9pv6c42j31kf0u013x.jpg)

成功运行 HelloWorld 即说明 Kotlin 环境已经搭建完成，接下来我们开始 ReadHub 项目开发之旅。