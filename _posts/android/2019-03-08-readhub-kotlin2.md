---
layout: blog 
android: true 
istop: false
title: "ReadHub项目Kotlin版开发指南(二、数据库和网络请求)" 
background-image: https://ws4.sinaimg.cn/large/006tKfTcly1g0wu7euc67j31900u04qp.jpg
date:  2019-03-08
category: Android
tags: 
- Android


---

# ReadHub项目Kotlin版转换指南(二、数据库和网络请求)

## 数据库

改造数据库过程中，参考了`Anko` 依赖库的集成方式，感觉用起来还是 `Greendao` 舒服，所以在此项目中依旧采用 `Greendao` 数据库。

`Kotlin` 中使用`Greendao` 目前还存在一些问题，主要为一心两项：

- Bean 类必须使用 java 文件
- `DaoMaster` 和 `DaoSession` 获取也必须使用 java 文件

##  网络请求

网络请求模块的转换中，主要遇到的问题是泛型不适配。

```kotlin
private val mRetrofitServiceHashMap = mutableMapOf<Class<*>, Retrofit>()
private val cachedApis = ConcurrentHashMap<Class<*>, Any?>()

    fun <T> getService(clz: Class<T>): T? {
        val obj = cachedApis[clz]

        if (obj != null) {
            return obj as T?
        } else {
            val retrofit = mRetrofitServiceHashMap[clz]

            retrofit.let {
                val service = retrofit?.create(clz)
                cachedApis[clz] = service
                return service
            }
        }
    }
```

```java
private HashMap<Class, Retrofit> mRetrofitServiceHashMap = new HashMap<>();
private ConcurrentHashMap<Class, Object> cachedApis = new ConcurrentHashMap<>();

	public <T> T getService(Class<T> clz) {
        Object obj = cachedApis.get(clz);
        if (obj != null) {
            return (T) obj;
        } else {
            Retrofit retrofit = mRetrofitServiceHashMap.get(clz);
            if (retrofit != null) {
                T service = retrofit.create(clz);
                cachedApis.put(clz, service);
                return service;
            } else {
                return null;
            }
        }
    }
```

