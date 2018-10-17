---
layout: blog 
android: true 
istop: false
title: "DexClassLoader的调用方法" 
background-image: https://ws1.sinaimg.cn/large/006tNbRwly1fwb2skt5dhj31kw11xkjn.jpg
date:  2018-10-17
category: Android
tags: 
- Android

---

**（注：本文摘自《Android内核剖析》--柯元旦）**

## DexClassLoader的使用方法

假设有两个Apk，Host.apk和Plugin.apk，其中Plugin定义了一个类PluginClass，该类中有一个方法functionAdd()，其代码如下：

```java
public class PluginClass {
    public PluginClass() {
        Log.i("PluginClass", "initialized PluginClass");
    }
    
    public int functionAdd(int a, int b) {
        return a + b;
    }
}
```

Host中为了调用functionAdd()方法，需要使用DexClassLoader去解析Plugin，具体调用方法如下：

```java
public void useDexClassLoader() {
    Intent intent = new Intent("com.example.plugin", null);
    PackageManager pm = getPackageManager();
    final List<ResolveInfo> plugins = pm.queryIntentActivities(intent, 0);
    ResolveInfo rinfo = plugins.get(0);
    ActivityInfo ainfo = rinfo.activityInfo;
    
    String div = System.getProperty("path.separator");
    String packageName = ainfo.packageName;
    String dexPath = ainfo.applicationInfo.sourceDir;
    String dexOutputDir = getApplicationInfo().dataDir;
    String libPath = ainfo.applicationInfo.nativeLibraryDir;
    
    DexClassLoader cl = new DexClassLoader(dexPath, dexOutputDir, libPath,                                       this.getClass().getClassLoader());
    try {
        //装载指定的类
        Class<?> clazz = cl.loadClass(packageName + ".PluginClass");
        
        //调用PluginClass的构造方法，返回PluginClass对象
        Object obj = clazz.newInstance();
        
        //通过反射获取functionAdd()方法和其参数
        Class[] params = new Class[2];
        params[0] = Integer.TYPE;
        params[1] = Integer.TYPE;
        Method action = clazz.getMethod("functionAdd", params);
        
        //执行方法
        Integer ret = (Integer)action.invoke(obj, 12, 34);
        Log.i("Host", "return value is " + ret);
    }
}
```

DexClassLoader构造函数的参数意义：

- dexPath，目标类Apk所在的路径。例如/data/data/com.example.plguin。如果包含多个路径，可使用特定分隔符分隔，并使用System.getProperty("path.separtor")获得。

- dexOutputDir，解压出的dex文件存放路径。

- libPath，目标类中所使用的c/c++库存放的路径。

- 最后一个参数指该装载器的父装载器，一般为当前执行类的装载器。
