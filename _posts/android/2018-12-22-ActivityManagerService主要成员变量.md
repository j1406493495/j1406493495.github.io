---
layout: blog 
android: true 
istop: false
title: "ActivityManagerService主要成员变量" 
background-image: https://ws1.sinaimg.cn/large/006tNbRwly1fyfulhkibij31hy0u0te0.jpg
date:  2018-12-22
category: Android
tags: 
- Android


---

## ActivityManagerService主要成员变量

### ProcessRecord进程数据类

![](https://ws3.sinaimg.cn/large/006tNbRwly1fyfu5msknjj319g0mwwlp.jpg)

### ActivityRecord数据类

![](https://ws3.sinaimg.cn/large/006tNbRwly1fyfu6pcwyaj318w09swka.jpg)

### TaskRecord类

![](https://ws1.sinaimg.cn/large/006tNbRwly1fyfu7e1emcj319608kwh2.jpg)

### 重要调度相关变量

##### 系统常量

- static final int MAX_ACTIVITIES = 20；

  后台Activity超过该常量，会强制杀死一些优先级较低的Activity。

- static final int MAX_RECENT_TASKS = 20;

  AMS会记录最近启动的Activity，但只记录20个。

- static final int PAUSE_TIMEOUT = 500;

  AMS通知应用暂停时，超过500ms无反应，AMS会强制关闭该Activity。

- static final int LAUNCH_TIMEOUT = 10 * 1000;

  AMS通知应用启动某个Activity时，如果超过10s，AMS就会放弃。

- static final int PROC_START_TIMEOUT = 10 * 1000;

  某个客户进程启动后，必须在10s内报告给AMS，否则AMS会认为该客户端不存在。

##### 等待序列

- final ArrayList mHistory = new ArrayList();

  该变量保存正在运行的Activity。

- private final ArrayLIst mLRUActivities = new ArrayList();

  最近所有的Activity列表。

- final ArrayList<PendingActivityLaunch> mPendingActivityLaunchers;

  预启动的Activity。

- final ArrayList<ActivityRecord> mStoppingActivities;

  待关闭的Activity。

- final ArrayList mFinishingActivities = new ArrayList();

  Activity处于finish时，不会立即回收，会保存到该变量中，达到警戒线才会回收。

##### 当前不同状态的ActivityRecord

- ActivityRecord mPauseingActivity = null；

  正在暂停的Activity。

- ActivityRecord mResumedActivity = null;

  正在运行的Activity。

- ActivityRecord mFocusedActivity = null;

  AMS通知WMS应该和用户交互的Activity。

- ActivityRecord mLastPausedActivity = null;

  上一次暂停的Activity。

