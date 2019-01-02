---
layout: blog 
android: true 
istop: false
title: "Android 生命周期" 
background-image: https://ws1.sinaimg.cn/large/006tNbRwly1fysliupeqdj319c0u011p.jpg
date:  2019-01-02
category: Android
tags: 
- Android


---

## Android 生命周期

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
