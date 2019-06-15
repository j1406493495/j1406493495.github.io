---
layout: blog 
android: true 
istop: true
title: "Input系统分析" 
background-image: https://i.loli.net/2019/06/15/5d04c06c510df73388.png
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
