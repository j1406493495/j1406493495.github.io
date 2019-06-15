---
layout: blog 
android: true 
istop: false
title: "PackageManagerService分析" 
background-image: https://i.loli.net/2019/06/15/5d04bcb5b877552487.jpg
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



