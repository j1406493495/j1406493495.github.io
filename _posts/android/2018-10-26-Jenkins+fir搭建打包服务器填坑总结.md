---
layout: blog 
android: true 
istop: false
title: "Windows环境Jenkins+fir搭建打包服务器填坑总结" 
background-image: https://ws1.sinaimg.cn/large/006tNbRwly1fwlpgbu9mzj31kw10tkjr.jpg
date:  2018-10-26
category: Android
tags: 
- Android

---

**【1，Jenkins安装步骤Getting Started长时间无反应】**

![gettingstarted](https://ws3.sinaimg.cn/large/006tNbRwly1fwlpjjgvfuj30re0i540b.jpg)

**【解决方案】**

1. 打开“cmd”，输入“services.msc”；
2. 找到Jenkins服务，右键选择“重新启动”；
3. 重新进入[localhost:8080](http://localhost:8080/)，发现即可跳过该步骤；
4. 安装失败的插件，在“系统管理—插件管理”中手动安装。

**【2，配置git仓库时验证总是失败】**

Failed to connect to repository : Command "D:\Git\bin\git.exe ls-remote -h git@47.***.git HEAD" returned status code 128:

stdout: 
stderr: Host key verification failed. 
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

**【解决方案】**

添加Credentials认证信息，可添加username and password来认证。

**【3，连接GitLab服务器失败：】**

Client error: HTTP 500 Internal Server Error

**解决方案**

![](https://ws4.sinaimg.cn/large/006tNbRwly1fwlpyrxgnaj31e80hadhu.jpg)

如上图所示，URL地址必须为http形式，不能为ssh。

**【4，Error: java.util.concurrent.ExecutionException: com.android.tools.aapt2.Aapt2Exception: AAPT2 error: check logs for details】**

**【解决方案】**

在gradle.properties文件结尾添加以下两行：

```groovy
android.enableBuildCache=false
android.enableAapt2=false
```

*注意：要在本地项目添加，然后上传到GitLab服务器，不能直接在Jenkins服务器代码里修改*

**【5，Error: java.util.concurrent.ExecutionException: com.android.builder.internal.aapt.AaptException: 
:app:mergeDebugResources FAILED】**

**【解决方案】**

打开AndroidStudio，导入**Jenkins服务器目录中**的项目，进行本地编译直到通过；

在Jenkins中该项目的配置中**移除app:clean**，再重新构建项目即可。

**【6，No toolchains found in the NDK toolchains folder for ABI with prefix: mips64el-linux-android】**

在第5个错误，进行本地编译的时候，出现了这个问题。

**【解决方案】**

这个问题出现是由于Android/Sdk/ndk-bundle中缺少mips64el-linux-android目录，从网上下载拷贝一个即可。

**【7，Deployment failed : Error while get details for update id = com.bsrobots.boxlunch  Build step 'Upload to fir.im' marked build as failure】**

配置项目打包上传到fir.im后报错

**【解决方案】**

打开fir.im官网，手动上传该应用，之后在Jenkins中构建项目就没问题了。