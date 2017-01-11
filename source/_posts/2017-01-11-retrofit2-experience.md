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
> 注：本篇是对Retrofit2.0的全面体验，并未涉及RxJava。

[demo.retrofit传送门](https://github.com/drawf/demo.retrofit)

![](http://7sbl4z.com1.z0.glb.clouddn.com/demo.retrofit/blob/master/images/screenshot_1.png?imageMogr2/thumbnail/250x)

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

### 代码实践与记录

#### 同步和异步请求
> 一个完整的请求包含以下几个步骤

1. 构建Retrofit对象

    ```Java
    Retrofit retrofit = new Retrofit.Builder()
                    //用于请求的HTTP client，设置OkHttpClient，有默认值。该方法是引用传递，对client的修改会影响后续请求。
                    .client(client)
                    .baseUrl(MovieService.BASE_URL)//设置baseUrl
                    .addConverterFactory(GsonConverterFactory.create())
                    //是否在调用create(Class)时检测接口定义是否正确，而不是在调用方法才检测，在开发、测试时使用。
                    .validateEagerly(BuildConfig.DEBUG)
                    .build();
    ```

2. 以interface的方式定义API

    ```Java
    public interface MovieService {

        String BASE_URL = "https://api.douban.com/v2/movie/";

        @GET("top250")
        Call<JsonObject> getTopMovie(@Query(value = "start", encoded = true) int start, @Query("count") int count);

    }
    ```

    这里需要注意的是

3. balabala















