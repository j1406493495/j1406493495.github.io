---
layout: blog 
android: true 
istop: true
title: "PackageManagerService分析" 
background-image: https://ws3.sinaimg.cn/large/006tNbRwly1fw8z507bogj31jk1xgx41.jpg
date:  2018-10-15
category: Android
tags: 
- Android
---

## installd进程

### initialize_globals

### initialize_directories

### execute

## installer系统服务

### 启动

```java
Installer installer = mSystemServiceManager.startService(Installer.class);
```

### 连接installed

## PackageManagerService分析

### 初始化

```java
 // Start the package manager.
        traceBeginAndSlog("StartPackageManagerService");
        mPackageManagerService = PackageManagerService.main(mSystemContext, installer,
                mFactoryTestMode != FactoryTest.FACTORY_TEST_OFF, mOnlyCore);
        mFirstBoot = mPackageManagerService.isFirstBoot();
        mPackageManager = mSystemContext.getPackageManager();
        Trace.traceEnd(Trace.TRACE_TAG_SYSTEM_SERVER);
```



