---
layout: blog 
android: true 
istop: false
title: "Android 生命周期" 
background-image: https://i.loli.net/2019/06/15/5d04c0a72802199062.jpg
date:  2019-01-02
category: Android
tags: 
- Android


---

## Android 生命周期

![](https://i.loli.net/2019/06/15/5d04c0b9af44765323.jpg)

- 首次启动

    onCreate()->onStart()->onResume()。

- 按下返回键

    onPause()->onStop()->onDestory()。

- 按下Home键

    onPause()->onSaveInstanceSave()->onStop()。

- 再次打开

    onRestart()->onStart()->onResume()。

- 屏幕旋转

    onPause()->onSaveInstanceState()->onStop()->onDestory()->onCreate()->onStart()

    ->onRestoreInstanceState()->onResume()。

### 非常用生命周期

- onContentChanged()

    当Activity的布局改动时，即setContentView()或者addContentView()方法执行完毕时就会调用该方法。

- onPostCreate()

    onPostCreate方法是指onCreate方法彻底执行完毕的回调，在onStart()后执行。

- onPostResume()

    onPostCreate方法是指onResume方法彻底执行完毕的回调。
