---
layout: blog 
android: true 
istop: false
title: "ReadHub项目Java转Kotlin填坑指南(一)" 
background-image: https://ws2.sinaimg.cn/large/006tNc79ly1fzdaksqgo0j31930u0n40.jpg
date:  2019-01-20
category: Android
tags: 
- Android


---

## ReadHub项目Java转Kotlin爬坑指南(一、环境转换)

### ReadHub项目简介

[ReadHub](https://github.com/j1406493495/ReadHub)是冯大辉[ReadHub资讯](https://readhub.me/)的第三方客户端，由官方提供Api支持，Woong独立开发的Android客户端。

### Java版本

Java版本已在Github开源，项目地址：https://github.com/j1406493495/ReadHub。

提供资讯阅读、稍后读、截图分享等功能。

### Kotlin版本

自从Google宣布Kotlin作为Android的官方开发语言后，Kotlin在国内的热度在不断提高，我们很有必要去了解Kotlin。

相对于干巴巴的去阅读Kotlin语法，实战总是最快的掌握方式。

有两条路摆在眼前，

> **第一条**，用Kotlin重写ReadHub项目，搭建环境，从零开始；
>
> **第二条**，在Java版本的基础上，将Java项目逐步转化为Kotlin项目。

于是乎，不想从零开始的我，选择了第二种方式，然后就发现自己掉进了一个巨坑。。。

下面开始，就是我的爬坑指南。。。

![](/Users/wong/Library/Application Support/typora-user-images/image-20190120203116640.png)

#### Kotlin plugin下载

![](https://ws4.sinaimg.cn/large/006tNc79ly1fzdcc4l01dj31ad0u0wjb.jpg)

#### project中的build.gradle

![](https://ws1.sinaimg.cn/large/006tNc79ly1fzdcgi7aicj312a0qidj3.jpg)

#### app中的build.gradle

![](https://ws2.sinaimg.cn/large/006tNc79ly1fzdcm94t8oj30xp0u0n3y.jpg)

环境搭建完成，下面就让我们开始愉快地转化项目吧。

