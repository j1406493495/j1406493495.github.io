---
layout: blog 
android: true 
istop: true 
title: "RxPermissions引发的血案——onActivityResult封装" 
background-image: https://ws4.sinaimg.cn/large/006tNc79gy1fp15k0bwqkj31fm0v8n67.jpg
date:  2018-03-04
category:  Android
tags: 
- Android
---

*注意：阅读本文需要一定的```RxJava```基础*

#### 简介

自从Android6.0开始，系统增加了动态权限控制，官方提供方法是通过`requestPermissions(String[] permissions, int requestCode);`方法请求权限, 并在`onRequestPermissionsResult(int requestCode,String[] permissions,int[] grantResults)`回调方法中处理请求结果。

但使用官方提供方法有几点让人很是烦恼：

>   一来每次请求权限都需定义```requestCode```，大家都知道变量命名是一件让人捉急的事情，
>
>   二来需要重写```onRequestPermissionResult```，不利于代码的阅读及维护，想想每次修改权限都要在函数间跳跃就有一种要砸电脑的冲动（心疼MBP一秒）。

笔者自己除了原生之外，还使用过```EasyPermission```和```RxPermission```，相比之下，```RxPermission```方便很多很多，使用RxPermission无需自定义```requestCode```和```onRequestPermissionnResult```回调，只需优雅的利用```RxJava```的链式调用即可一步到位解决权限动态请求。

一步到位？不用定义```requestCode```和```onRequestPermissionnResult```，有这么好的事情？

（福尔摩斯登场）那么如果```onActivityResult```也采用这种方式，是不是也可以远离```requestCode```和```onActivityResult```了？

前提有了，接下来我们就来搞点事情，鼓捣鼓捣。。。

#### RxPermissions分析

```
RxJavaInterop.toV2Observable(
        RxView.clicks(findViewById(R.id.ensureEach))
                .map(new Func1<Void, Object>() {
                    @Override
                    public Object call(final Void aVoid) {
                        return new Object();
                    }
                })
)
        .compose(rxPermissions.ensureEach(Manifest.permission.CAMERA,
                Manifest.permission.RECORD_AUDIO,
                Manifest.permission.READ_EXTERNAL_STORAGE,
                Manifest.permission.WRITE_EXTERNAL_STORAGE))
        .subscribe(new Consumer<Permission>() {
                       @Override
                       public void accept(Permission permission) {
                           if (permission.shouldShowRequestPermissionRationale) {
                               Log.d("ensureEach === ", " should ==== ");
                           } else if (permission.granted) {
                               Log.d("ensureEach === ", " granded ==== ");
                           } else {
                               Log.d("ensureEach === ", " default === ");
                           }
                       }
                   },
                new Consumer<Throwable>() {
                    @Override
                    public void accept(Throwable t) {
                        Log.e(TAG, "onError", t);
                    }
                },
                new Action() {
                    @Override
                    public void run() {
                        Log.i(TAG, "OnComplete");
                    }
                });
```

是不是很简单，一步到位，省下来的时间都足够各位大神吃鸡了。

那么```requestCode```和```onRequestPermissionnResult```去哪里了呢？核心在于```RxPermission```内部封装了一个```Fragment```，请求和回调权限均放在其中处理。

RxPermission源码包含三个类，```RxPermissions.java```、```Permission.java```和```RxPermissionsFragment.java```。其中```RxPermissions.java```负责与外界的调用关系，包括创建实例，多种请求模式维护；```Permission.java```负责保存权限的名称和状态；```RxPermissionsFragment.java```负责发起权限请求和回调。

```
public RxPermissions(@NonNull Activity activity) {
    mRxPermissionsFragment = getRxPermissionsFragment(activity);
}

private RxPermissionsFragment getRxPermissionsFragment(Activity activity) {
    RxPermissionsFragment rxPermissionsFragment = findRxPermissionsFragment(activity);
    boolean isNewInstance = rxPermissionsFragment == null;
    if (isNewInstance) {
        rxPermissionsFragment = new RxPermissionsFragment();
        FragmentManager fragmentManager = activity.getFragmentManager();
        fragmentManager
                .beginTransaction()
                .add(rxPermissionsFragment, TAG)
                .commitAllowingStateLoss();
        fragmentManager.executePendingTransactions();
    }
    return rxPermissionsFragment;
}

private RxPermissionsFragment findRxPermissionsFragment(Activity activity) {
    return (RxPermissionsFragment) activity.getFragmentManager().findFragmentByTag(TAG);
}
```

可以看出```new RxPermissions(MainActivity.this)```时根据传了的```Activity```会创建一个```Fragment```，在这个Fragment中处理动态权限请求。

#### onActivityResult封装

仿照RxPermissions中封装Fragment发起请求的方式，对startActivityForResult和onActivityResult进行封装，即为今天我要介绍的项目[ResultCallBack](https://github.com/j1406493495/ResultBack)，Github地址：https://github.com/j1406493495/ResultBack。

其中master分支为非RxJava版本，rxjava分支为RxJava版本。

#####非RxJava版本使用

添加依赖：

```
dependencies {
    compile "com.woong:ResultBack:1.0.0"
}
```

使用方式：

```
mResultBack = new ResultBack(this);

btnSuccess.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Intent intent = new Intent(MainActivity.this, ResultSuccessActivity.class);
        intent.putExtra("success", false);

        mResultBack.startForResult(intent, new ResultBack.Callback() {
            @Override
            public void onActivityResult(ResultInfo resultInfo) {
                int resultCode = resultInfo.getResultCode();
                Intent data = resultInfo.getData();

                if (resultCode == RESULT_OK && data != null) {
                    boolean result= data.getBooleanExtra("result", false);
                    Toast.makeText(MainActivity.this, "result == " + result, Toast.LENGTH_SHORT).show();
                }
            }
        });
    }
});
```

##### ResultBack源码分析

###### 创建实例

```
public ResultBack(Activity activity) {
    mResultBackFragment = getResultBackFragment(activity);
}

private ResultBackFragment getResultBackFragment(Activity activity) {
    ResultBackFragment resultBackFragment = findResultBackFragment(activity);
    if (resultBackFragment == null) {
        resultBackFragment = new ResultBackFragment();
        FragmentManager fragmentManager = activity.getFragmentManager();
        fragmentManager
                .beginTransaction()
                .add(resultBackFragment, TAG)
                .commitAllowingStateLoss();
        fragmentManager.executePendingTransactions();
    }
    return resultBackFragment;
}

private ResultBackFragment findResultBackFragment(Activity activity) {
    return (ResultBackFragment) activity.getFragmentManager().findFragmentByTag(TAG);
}
```

ResultBack创建实例部分同RxPermissions，从getResultBackFragment方法可知若传入的Activity中没有用来请求startActicityForResult的Fragment，则创建一个与之对应的Fragment，该Fragment负责对应Activity中的请求。

###### Fragment处理onActivityResult

```
private Map<Integer, ResultBack.Callback> mCallbacks = new HashMap<>();

public void startForResult(Intent intent, ResultBack.Callback callback) {
    mCallbacks.put(callback.hashCode(), callback);
    startActivityForResult(intent, callback.hashCode());
}

@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    //callback方式的处理
    ResultBack.Callback callback = mCallbacks.remove(requestCode);
    if (callback != null) {
        callback.onActivityResult(new ResultInfo(resultCode, data));
    }
}
```

```
public interface Callback {
    void onActivityResult(ResultInfo resultInfo);
}
```

```
public ResultInfo(int resultCode, Intent data) {
    this.resultCode = resultCode;
    this.data = data;
}
```

创建一个Map存储Fragment中所有Callback，Callback接口中onActivityResult方法回调ResultInfo类，该类包含resultCode和Intent。

由startForResult方法发起请求，其中以callback.hashCode()为key，callback为value存入map；

Fragment中onActivityResult得到请求结果，根据requestCode从map中获取对应的callback并完成请求结果回调。

###### RxJava版本使用

添加依赖：

```
dependencies {
    compile "com.woong:ResultBack-RxJava:1.0.0"
}
```

使用方式：

```
RxView.clicks(btnSuccess)
        .map(new Function<Object, Intent>() {
            @Override
            public Intent apply(Object o) throws Exception {
                Intent intent = new Intent(MainActivity.this, ResultSuccessActivity.class);
                intent.putExtra("success", false);
                return intent;
            }
        })
        .flatMap(new Function<Intent, Observable<ResultInfo>>() {
            @Override
            public Observable<ResultInfo> apply(Intent intent) throws Exception {
                return mResultBack.startForResult(intent);
            }
        })
        .subscribe(new Consumer<ResultInfo>() {
            @Override
            public void accept(ResultInfo resultInfo) throws Exception {
                int resultCode = resultInfo.getResultCode();
                Intent data = resultInfo.getData();

                if (resultCode == RESULT_OK && data != null) {
                    boolean result= data.getBooleanExtra("result", false);
                    Toast.makeText(MainActivity.this, "result == " + result, Toast.LENGTH_SHORT).show();
                }
            }
        });
```

#### 总结

至此，onActivityResult封装就结束了。

阅读Github优质开源项目的源码，从中提取精华代码，并运用的实际项目中，是一种开发进阶的好方法，上述源码分析中有任何错误的地方，还请大家多多反馈，共同学习进步。
