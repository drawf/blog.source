---
layout: post
title: "全面体验Retrofit2.0"
categories: [tech]
date: 2017-01-11
tags: [Retrofit2.0]
toc: true
description: 官方标语：一个用于Android和Java平台的类型安全的网络框架。
---

### 放在前边
> 注：本篇只是对Retrofit2.0的全面体验，并未涉及RxJava。

[demo.retrofit传送门](https://github.com/drawf/demo.retrofit)

![](https://github.com/drawf/demo.retrofit/blob/master/images/screenshot_1.png?raw=true)

### Retrofit2.0
Slogan:A type-safe HTTP client for Android and Java.

Retrofit is a type-safe REST client for Android built by Square. The library provides a powerful framework for authenticating and interacting with APIs and sending network requests with OkHttp.

官方标语：一个用于Android和Java平台的类型安全的网络框架。

Retrofit是一个Square开发的类型安全的REST安卓客户端请求库。这个库为网络认证、API请求以及用OkHttp发送网络请求提供了强大的框架 。

### Gradle配置
```gradle
compile 'com.google.guava:guava:20.0'
compile 'com.squareup.retrofit2:retrofit:2.1.0'
compile 'com.squareup.retrofit2:converter-gson:2.1.0'
```

Google Guava库是一个非常优秀的包含很多Java工具类集的库，广泛使用在Google公司内部，因此它可以被使用到几乎所有的Java项目中。更多使用姿势，大家自行google，未来我也会整理出关于Guava的博客。
