---
layout: blog 
android: true 
istop: false
title: "Android 常用文件系统" 
background-image: https://ws2.sinaimg.cn/large/006tNc79ly1g1xyjtp4hcj30mi0y64qp.jpg
date:  2019-04-10
category: Android
tags: 
- Android


---

## /system

`system` 分区用来存放谷歌或者厂家提供的组件。

该分区中的内容都属于 `root:root`，权限为775。

### /system/app

存放系统应用。

### /system/bin

常用的二进制执行文件，即各种命令，如 `ls`、`cp` 等。

### /system/xbin

非常用的二进制执行文件，如 `su` 等。

### /system/framework

存放源码中 `framework` 模块中编译出来的各种 `jar` 包，如 `service.jar`、`framework-res.jar` 等。

### /system/etc

存放各种配置文件。

### build.prop 文件

记录一些属性，可通过 `getprop` 命令获取。

## /data

`data` 分区存放所有用户的个人数据，恢复出厂设置即格式化该分区。

### /data/app

存放用户安装的 `app`。

### /data/data/xxx

用户安装的应用数据。

### /data/property

存放持久化属性，重启后仍然保留。文件名即属性名，通过 `getprop` 命令获取。

### /data/anr

发生 `anr` 时存放日志的目录。

### /data/tombstones

发生 `native` 崩溃时存放日志的目录，`tombstone_00～tombstone_09`。




