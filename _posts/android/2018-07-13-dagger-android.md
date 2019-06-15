---
layout: blog 
android: true 
istop: false
title: "dagger Apt源码图谱" 
background-image: https://i.loli.net/2019/06/15/5d04aa8aa52ef43878.jpg
date:  2018-07-13
category: Android 
tags: 
- Android 
---



## Dagger apt源码图谱

![dagger](https://i.loli.net/2019/06/15/5d04aa9fcc79279957.jpg)

`SimpleModule_ProviderStudentFactory` 类负责创建 `Student` 对象；

`SimpleActivity_MembersInjector` 类负责将 `Student` 对象赋值到对应的成员变量；

`DaggerSimpleComponent` 初始化上述两个类，并作为这两个类的桥梁。















