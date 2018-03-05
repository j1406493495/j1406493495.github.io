---
layout: blog 
android: true 
istop: true 
title: "RxPermissions源码引发的血案——onActivityResult封装" 
background-image: https://ws4.sinaimg.cn/large/006tNc79gy1fp15k0bwqkj31fm0v8n67.jpg
date:  2018-03-04
category:  Android
tags: 
-  Android
---

*注意：阅读本文需要一定的```RxJava```基础*

#### 简介

自从Android6.0开始，系统增加了动态权限控制，官方提供方法是通过`requestPermissions(String[] permissions, int requestCode);`方法请求权限, 并在`onRequestPermissionsResult(int requestCode,String[] permissions,int[] grantResults)`回调方法中处理请求结果。

但使用官方提供方法有几点让人很是烦恼：

> 一来每次请求权限都需定义```requestCode```，大家都知道变量命名是一件让人捉急的事情，
>
> 二来需要重写```onRequestPermissionResult```，不利于代码的阅读及维护，想想每次修改权限都要在函数间跳跃就有一种要砸电脑的冲动（心疼MBP一秒）。

笔者自己除了原生之外，还使用过```EasyPermission```和```RxPermission```，相比之下，```RxPermission```方便很多很多，使用RxPermission无需自定义```requestCode```和```onRequestPermissionnResult```回调，只需优雅的利用```RxJava```的链式调用即可一步到位解决权限动态请求。

一步到位？不用定义```requestCode```和```onRequestPermissionnResult```，有这么好的事情？

（福尔摩斯登场）那么如果```onActivityResult```也采用这种方式，是不是也可以远离```requestCode```和```onActivityResult```了？

前提有了，接下来我们就来搞点事情，鼓捣鼓捣。。。

#### RxPermissions源码分析

##### RxPermission简单使用

```
new RxPermissions(MainActivity.this)
	.request(Manifest.permission.CAMERA,
        Manifest.permission.RECORD_AUDIO,
        Manifest.permission.CALL_PHONE)
.subscribe(new Consumer<Boolean>() {
    @Override
    public void accept(Boolean granted) throws Exception {
        // Android 6.0前的设备默认返回true
        if (granted) {
        	//权限允许处理    
        } else {
            //权限拒绝处理
        }
    }
});
```

是不是很简单，一步到位，省下来的时间都足够各位大神吃鸡了。

那么```requestCode```和```onRequestPermissionnResult```去哪里了呢？核心在于```RxPermission```内部封装了一个```Fragment```，请求和回调权限均放在其中处理。

RxPermission源码包含三个类，RxPermissions.java、Permission.java和RxPermissionsFragment.java。其中RxPermissions.java负责与外界的调用关系，包括创建实例，多种请求模式维护；Permission.java负责保存权限的名称和状态；RxPermissionsFragment.java负责发起权限请求和回调。

##### 创建实例

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

##### 三种请求模式

![](https://ws2.sinaimg.cn/large/006tNc79gy1foyfjcy5duj31as0c0406.jpg)

```
@SuppressWarnings({"WeakerAccess", "unused"})
public Observable<Boolean> request(final String... permissions) {
	return Observable.just(TRIGGER).compose(ensure(permissions));
}
```

可知，requestXXX()实际调用ensureXXX()，所以我们直接分析ensureXXX()方法：

- ensureEach(requestEach)

  ```
  @SuppressWarnings("WeakerAccess")
  public <T> ObservableTransformer<T, Permission> ensureEach(final String... permissions) {
  	return new ObservableTransformer<T, Permission>() {
  		@Override
  		public ObservableSource<Permission> apply(Observable<T> o) {
  			return request(o, permissions);
  		}
  	};
  }
  ```

  ensureEach模式依次回调每个权限的请求结果。

- ensure(request)

  ```
  @SuppressWarnings("WeakerAccess")
  public <T> ObservableTransformer<T, Boolean> ensure(final String... permissions) {
  	return new ObservableTransformer<T, Boolean>() {
  		@Override
  		public ObservableSource<Boolean> apply(Observable<T> o) {
  			return request(o, permissions)
  				.buffer(permissions.length)
  				.flatMap(new Function<List<Permission>, ObservableSource<Boolean>>() 				 {
  					@Override
  					public ObservableSource<Boolean> apply(List<Permission> permissions) throws Exception {
  						if (permissions.isEmpty()) {
  							return Observable.empty();
  						}

  						for (Permission p : permissions) {
  							if (!p.granted) {
  								return Observable.just(false);
  							}
  						}
  						return Observable.just(true);
  					}
  				});
  		}
  	};
  }
  ```

  第6行request(o, permissions)方法，该方法内部调用Fragment的请求权限代码(分析见下文，这里主要讲请求模式)。

  第15～20行，即ensure模式的主要代码。可见批量请求代码时只要有一个权限被拒绝，则请求结果为false。

- ensureEachCombined(requestEachCombined)

  ```
  return Observable.just(new Permission(permissions));
  ```

  这里只附上关键代码，其余部分和上面一致。

  该模式具体实现的秘密就在new Permission(permission)中，

  ```
  public Permission(List<Permission> permissions) {
  	name = combineName(permissions);
  	granted = combineGranted(permissions);
  	shouldShowRequestPermissionRationale = 	
  		combineShouldShowRequestPermissionRationale(permissions);
  } 
  ```

  ```
  private String combineName(List<Permission> permissions) {
  	return Observable.fromIterable(permissions)
  		.map(new Function<Permission, String>() {
  			@Override
  			public String apply(Permission permission) throws Exception {
  				return permission.name;
  			}
  		}).collectInto(new StringBuilder(), new BiConsumer<StringBuilder, String>() 		{
  			@Override
  			public void accept(StringBuilder s, String s2) throws Exception {
  				if (s.length() == 0) {
  					s.append(s2);
  				} else {
  					s.append(", ").append(s2);
  				}
  			}
  		}).blockingGet().toString();
  }

  private Boolean combineGranted(List<Permission> permissions) {
  	return Observable.fromIterable(permissions)
  		.all(new Predicate<Permission>() {
  			@Override
  			public boolean test(Permission permission) throws Exception {
  				return permission.granted;
  			}
  		}).blockingGet();
  }

  private Boolean combineShouldShowRequestPermissionRationale(List<Permission> permissions) {
  	return Observable.fromIterable(permissions)
  		.any(new Predicate<Permission>() {
  			@Override
  			public boolean test(Permission permission) throws Exception {
  				return permission.shouldShowRequestPermissionRationale;
  			}
  		}).blockingGet();
  }
  ```

  三个方法分别对权限名称、状态、是否继续弹出请求框进行了combine。

  ![](https://ws3.sinaimg.cn/large/006tNc79gy1foyfj2q52yj313e0im771.jpg)

  collectInto操作符将各权限名称进行拼接，并过滤重复权限。 ![](https://ws3.sinaimg.cn/large/006tNc79gy1foyfoeqyktj312w0howgz.jpg)

  all操作符过滤所有权限请求结果，只有当所有权限均granted为true的时候才会返回true。

  ![](https://ws4.sinaimg.cn/large/006tNc79gy1foyfogr30aj313q0is765.jpg)

  any操作符过滤所有继续弹出请求框，只要有一个请求需要继续弹出弹框，则回调permission.shouldShowRequestPermissionRationale。

##### 发起/回调权限请求

request(o, permissions)方法内部由requestImplementation方法具体实现权限请求：

```
@TargetApi(Build.VERSION_CODES.M)
Observable<Permission> requestImplementation(final String... permissions) {
	List<Observable<Permission>> list = new ArrayList<>(permissions.length);
	List<String> unrequestedPermissions = new ArrayList<>();

	for (String permission : permissions) {
		mRxPermissionsFragment.log("Requesting permission " + permission);
		if (isGranted(permission)) {
			list.add(Observable.just(new Permission(permission, true, false)));
			continue;
		}

		if (isRevoked(permission)) {
			list.add(Observable.just(new Permission(permission, false, false)));
			continue;
		}

		PublishSubject<Permission> subject = 					
			mRxPermissionsFragment.getSubjectByPermission(permission);
		if (subject == null) {
			unrequestedPermissions.add(permission);
			subject = PublishSubject.create();
			mRxPermissionsFragment.setSubjectForPermission(permission, subject);
		}

		list.add(subject);
	}

	if (!unrequestedPermissions.isEmpty()) {
		String[] unrequestedPermissionsArray = unrequestedPermissions.toArray(new 		
			String[unrequestedPermissions.size()]);
			requestPermissionsFromFragment(unrequestedPermissionsArray);
	}
	return Observable.concat(Observable.fromIterable(list));
}
```

第6～26行筛选出需要请求的权限列表，第32行开始由Fragment发起权限请求。

```
@TargetApi(Build.VERSION_CODES.M)
void requestPermissions(@NonNull String[] permissions) {
	requestPermissions(permissions, PERMISSIONS_REQUEST_CODE);
}
```

```
void onRequestPermissionsResult(String permissions[], int[] grantResults, boolean[] shouldShowRequestPermissionRationale) {
	for (int i = 0, size = permissions.length; i < size; i++) {
		log("onRequestPermissionsResult  " + permissions[i]);
		// Find the corresponding subject
		PublishSubject<Permission> subject = mSubjects.get(permissions[i]);
		if (subject == null) {
			// No subject found
			return;
		}
		mSubjects.remove(permissions[i]);
		boolean granted = grantResults[i] == PackageManager.PERMISSION_GRANTED;
		subject.onNext(new Permission(permissions[i], granted, 	
			shouldShowRequestPermissionRationale[i]));
		subject.onComplete();
	}
}
```

上述两段代码即为Fragment中发起权限请求和请求回调的方法，第12～14行可看出权限请求回调后依次发送onNext和onComplete事件发送给下游接收者。



#### onActivityResult封装

仿照RxPermissions中封装Fragment发起请求的方式，对startActivityForResult和onActivityResult进行封装，即为今天我要介绍的项目[ResultCallBack](https://github.com/j1406493495/ResultBack)，Github地址：https://github.com/j1406493495/ResultBack。

其中master分支为非RxJava版本，rxjava分支为RxJava版本。

##### 非RxJava版本使用

添加依赖：

```
dependencies {
    compile "com.woong:ResultBack:1.0.0"
}
```

使用方式：

```
btnSuccess.setOnClickListener(new View.OnClickListener() {
	@Override
	public void onClick(View v) {
		Intent intent = new Intent(MainActivity.this, ResultSuccessActivity.class);
		intent.putExtra("success", false);

		new ResultBack(MainActivity.this).startForResult(intent, new ResultBack.Callback() {
			@Override
			public void onActivityResult(ResultInfo resultInfo) {
				int resultCode = resultInfo.getResultCode();
				Intent data = resultInfo.getData();

				if (resultCode == RESULT_OK && data != null) {
					boolean result= data.getBooleanExtra("result", false);
					Toast.makeText(MainActivity.this, "result == " + result, 		
						Toast.LENGTH_SHORT).show();
				}
			}
		});
	}
});
```

##### RxJava版本使用

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
				return new ResultBack(MainActivity.this).startForResult(intent);
			}
		})
		.subscribe(new Consumer<ResultInfo>() {
			@Override
			public void accept(ResultInfo resultInfo) throws Exception {
				int resultCode = resultInfo.getResultCode();
				Intent data = resultInfo.getData();

				if (resultCode == RESULT_OK && data != null) {
					boolean result= data.getBooleanExtra("result", false);
					Toast.makeText(MainActivity.this, "result == " + result, 
						Toast.LENGTH_SHORT).show();
				}
			}
		});
```



