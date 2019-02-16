---
layout: blog 
android: true 
istop: false
title: "dagger Apt源码图谱" 
background-image: https://ws4.sinaimg.cn/large/006tNc79gy1ft827p9rnmj31kw1z4x6q.jpg
date:  2018-07-13
category: Android 
tags: 
- Android 
---



## Dagger apt源码图谱

![dagger](https://ws4.sinaimg.cn/large/006tNc79gy1ft8261dya2j314m0vk78j.jpg)

`SimpleModule_ProviderStudentFactory` 类负责创建 `Student` 对象；

`SimpleActivity_MembersInjector` 类负责将 `Student` 对象赋值到对应的成员变量；

`DaggerSimpleComponent` 初始化上述两个类，并作为这两个类的桥梁。















