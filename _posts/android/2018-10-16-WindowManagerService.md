---
layout: blog 
android: true 
istop: true
title: "从变量角度分析WindowManagerService" 
background-image: https://ws3.sinaimg.cn/large/006tNbRwly1fw9vq0tpplj31kw11xhdx.jpg
date:  2018-10-16
category: Android
tags: 
- Android
---

## WindowManagerService(WMS)

WMS是管理Activiy所相应的窗口系统(系统窗口以及嵌套的子窗口)，还有跟App,SurfaceFlinger等模块间相互协同工作。

WMS在SystemServer.java中创建：

```java
 traceBeginAndSlog("StartWindowManagerService");
            wm = WindowManagerService.main(context, inputManager,
                    mFactoryTestMode != FactoryTest.FACTORY_TEST_LOW_LEVEL,
                    !mFirstBoot, mOnlyCore);
            ServiceManager.addService(Context.WINDOW_SERVICE, wm);
            ServiceManager.addService(Context.INPUT_SERVICE, inputManager);
            Trace.traceEnd(Trace.TRACE_TAG_SYSTEM_SERVER);
```

WMS继承自IWindowManager.Stub，熟悉AIDL的都知道，WMS将会作为Binder的服务端。

Activity通过其成员变量mWindowManager(数据类型WindowManagerImpl)，再调用WindowManagerGlobal对象，经过binder call跟WMS通信。

## mPolicy

实例对象为PhoneWindowManager,用于实现各种窗口相关的策略。

## mSessions

保存着所有的Session对象，同一进程只存在一个Session。

### Session

Session继承于`IWindowSession.Stub`, 作为Binder服务端。

App跟AMS通信，会建立Session连接到WMS，后续便通过IWindowSesson跟WMS通信。

当App需要执行和窗口管理相关的操作时，都是通过IPC调用Session对象中的相关方法实现的。

## mWindowMap

保存所有的WindowState对象;以IBinder为key, 是IWindow的Bp端。

### WindowState（Bp端）

WindowState对象代表一个窗口，记录在system_server。

mClient对象数据类型为IWindow，是app端的ViewRootImpl.W服务的binder代理对象，用于跟应用端交互；

mSession对象数据类型为Session，是system_server的Binder服务端；

mToken用于跟AMS交互。

### viewRootImpl（服务端）

管理DecorView跟WMS的交互；每次调用addView()添加窗口时，都会创建一个ViewRootImpl对象。

ViewRootImpl.W extends IWindow.Stub，app端创建的binder服务。

同一进程中所有的ViewRootImpl对象只对应唯一相同的Session代理对象。

## mTokenMap

保存所有的WindowToken对象; 以IBinder为key,可以是IAppWindowToken或者其他Binder的Bp端。

### WindowToken（Bp端）

IAppWindowToken或者其他Binder的Bp端。

### ActivityRecord（服务端）

ActivityRecord.Token extends IApplicationToken.Stub，ActivityRecord对象初始化时会创建数据类型为ActivityRecord.Token的成员变量appToken，然后会将该对象传递到ActivityThread。

## mChoreographer

用于控制窗口动画,屏幕旋转等操作

## mDisplayContents

记录一组DisplayContent对象,这个跟多屏输出相关

## 参考：

http://gityuan.com/2017/04/16/activity-with-window/

http://gityuan.com/2017/01/22/start-activity-wms/



