---
layout: blog 
android: true 
istop: true
title: "Input系统分析" 
background-image: https://ws3.sinaimg.cn/large/006tNbRwly1fw9vq0tpplj31kw11xhdx.jpg
date:  2018-10-20
category: Android
tags: 
- Android
---

## Java层InputManagerService

**采用android.display线程处理Message**

## JNI的NativeInputManager

**采用android.display线程处理Message,以及创建EventHub**

## Native的InputManager

**创建InputReaderThread和InputDispatcherThread两个线程**

### InputReader

### InputDispatcher

![https://ws2.sinaimg.cn/large/006tKfTcly1fto9lrsdrij31360pu459.jpg](https://ws4.sinaimg.cn/large/006tKfTcly1fto9nlufhhj31360pu0w5.jpg)