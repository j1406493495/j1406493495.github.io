---
layout: blog 
android: true 
istop: false
title: "Android 错误杂集" 
background-image: https://ws2.sinaimg.cn/large/006tNc79ly1fyx9r9zmdmj30mc0xi3zq.jpg
date:  2019-01-06
category: Android
tags: 
- Android


---

## Android 错误杂集

### 1、RxJava onComplete

当上游发送了一个onComplete后, 上游onComplete之后的事件将会`继续`发送, 而下游收到onComplete事件之后将`不再继续`接收事件.

当上游发送了一个onError后,  上游onError之后的事件将`继续`发送, 而下游收到onError事件之后将`不再继续`接收事件.

### 2、GreenDao toMany return null

```java
public class TeacherBean {
    @Id
    public long id;
    public String name;
    public int age;
    @ToMany(referencedJoinProperty = "teacherBeanId")
    public List<StudentBean> students;
}  

public class StudentBean {
    @Id
    public long id;
    public String name;
    public int age;
    public long teacherBeanId;
}
```

由于项目是使用了 `public` ，所以可以直接通过 `teacherBean.students` 调用，但是用该方式 `students` 返回 `null` ，需用 `teacherBean.getStudents()` 方式。

