---
layout: blog 
android: true 
istop: false
title: "PopupWindow创建流程" 
background-image: https://i.loli.net/2019/06/15/5d04bf1daa48642787.jpg
date:  2018-12-08
category: Android
tags: 
- Android


---

## PopupWindow创建流程

#### 构造方法

- mContext赋值
- mWindowManager赋值
- mContentView赋值，为窗口中真正显示的内容

#### 使用方法及注意点

```java
public void showAtLocation(View parent, int gravity, int x, int y)
public void showAsDropDown(View anchor, int xoff, int yoff, int gravity)
```

**PopupWindow添加的是纯粹的窗口，即不包含Window类中菜单，系统按键等。纯粹的窗口会将处理事件交给其内部的View/ViewGroup处理。**

