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
        Call<JsonObject> getTopMovie(@Query("start") int start, @Query("count") int count);

    }
    ```

    这里需要注意的是`BASE_URL`与`注解中的url`组合规则：

    ```
    BaseUrl ＋ 注解中的url ＝ 最终结果

    https://api.douban.com/v2/movie/ + top250 = https://api.douban.com/v2/movie/top250
    https://api.douban.com/v2/movie + top250 = https://api.douban.com/v2/top250
    https://api.douban.com/v2/movie/ + /top250 = https://api.douban.com/top250
    https://api.douban.com/v2/movie/ + https://github.com/drawf = https://github.com/drawf
    ```

3. 得到Call对象即可进行请求

   ```Java
   mMovieService = retrofit.create(MovieService.class);//创建定义API接口的实现
   mCall = mMovieService.getTopMovie(0, 2);//调用API得到Call对象

   mCall.cancel();//取消请求，mCall执行方法只能调用一次,否则会抛IllegalStateException
   mCall.request();//得到Request对象
   mCall.clone();//克隆一个实例
   mCall.isCanceled();//是否取消了请求
   mCall.isExecuted();//是否已执行或已入队列

   mCall.execute();//同步请求
   mCall.enqueue(Callback<T> callback);//异步请求
   ```

#### API的注解使用
Retrofit注解共22个，分三类介绍

1. HTTP请求方法

    <table><thead><tr class="thead-first-child"><th align="center"> 注解 </th><th align="center"> 说明 </th></tr></thead><tbody><tr class="tbody-first-child"><td align="center"> GET、POST、PUT、DELETE、<br>PATCH、HEAD、OPTIONS </td><td align="center"> 1，分别对应HTTP请求方法<br> 2，接受一个字符串作为注解中的url，默认为&quot;&quot;</td></tr><tr class="tbody-even-child"><td align="center"> HTTP </td><td align="center"> 可以替换上述7个方法，或者其它扩展方法 </td></tr></tbody></table>

    ```Java
    /*可以替代其它请求方法*/
    /*String method(); 请求方法，必须大写*/
    /*String path() default ""; 请求路径*/
    /*boolean hasBody() default false; 是否有请求体*/
    @HTTP(method = "GET", path = "top250", hasBody = false)
    Call<JsonObject> testHttp(@Query("start") int start, @Query("count") int count);
    ```

2. 参数类

    <table><thead><tr class="thead-first-child"><th align="center"> 注解 </th><th align="center"> 说明 </th></tr></thead><tbody><tr class="tbody-first-child"><td align="center"> Query、QueryMap </td><td align="center"> 用于GET的请求参数 </td></tr><tr class="tbody-even-child"><td align="center"> Url </td><td align="center"> 用全路径复写BaseUrl </td></tr><tr class="tbody-odd-child"><td align="center"> Path </td><td align="center"> 用于替换和动态更新URL的占位符 </td></tr><tr class="tbody-even-child"><td align="center"> Header、Headers </td><td align="center"> 用于添加请求头 </td></tr><tr class="tbody-odd-child"><td align="center"> Body </td><td align="center"> 用于POST、PUT、PATCH请求体 </td></tr><tr class="tbody-even-child"><td align="center"> Field、FieldMap </td><td align="center"> 用于form表单形式的键值对参数 </td></tr><tr class="tbody-odd-child"><td align="center"> Part、PartMap </td><td align="center"> 用于POST文件上传 </td></tr></tbody></table>

    ```Java
    /*@Query，@QueryMap 查询参数，用于GET查询，两者都可以约定是否需要encode，默认false*/
    /*@Query(value = "start", encoded = true) int start*/
    @GET("top250")
    Call<JsonObject> testQueryMap(@QueryMap(encoded = true) Map<String, Object> params);

    /*使用全路径复写baseUrl，用于非统一baseUrl的场景*/
    @GET
    Call<JsonObject> testUrl(@Url String url);

    /*URL占位符，用于替换和动态更新，相应的参数必须使用相同的字符串被@Path进行注释*/
    @GET("{type}")
    Call<JsonObject> testPath(@Path("type") String type, @Query("start") int start, @Query("count") int count);

    /*@Header，@Headers 不能被互相覆盖*/
    @Headers({
            "token:test override",
            "User-Agent: Wanzi-Retrofit-Sample-App"
    })
    @GET("top250")
    Call<JsonObject> testHeader(@Header("token") String token, @Query("start") int start, @Query("count") int count);

    /*用于POST、PUT、PATCH请求体，将实例对象根据GsonConverterFactory定义的转化方式转换为对应的json字符串参数*/
    /*@Body与@FormUrlEncoded、@Field不能同时使用*/
    @PUT("update")
    Call<JsonObject> testBody(@Body User user);

    /*@Field，@FieldMap 为form表单形式的键值对*/
    /*需要添加@FormUrlEncoded表示表单提交 Content-Type:application/x-www-form-urlencoded*/
    @FormUrlEncoded
    @POST("update")
    Call<JsonObject> testField(@Field("name") String name, @Field("age") int age);

    /*@Part，@PartMap 用于POST文件上传*/
    /*其中@Part MultipartBody.Part代表文件，@Part("key") RequestBody或其它类型代表参数*/
    /*需要添加@Multipart表示支持文件上传的表单，Content-Type: multipart/form-data*/
    @Multipart
    @POST("upload")
    Call<JsonObject> testPart(@Part("desc") String desc, @Part MultipartBody.Part file);
    ```

3. 标记类

    <table><thead><tr class="thead-first-child"><th align="center"> 注解 </th><th align="center"> 说明 </th></tr></thead><tbody><tr class="tbody-first-child"><td align="center"> FormUrlEncoded </td><td align="center"> 表示请求体是一个Form表单 </td></tr><tr class="tbody-even-child"><td align="center"> Multipart </td><td align="center"> 1，意思为多部分，表示请求体是一个支持文件上传的Form表单<br> 2，建议去深入了解HTTP协议</td></tr><tr class="tbody-odd-child"><td align="center"> Streaming </td><td align="center"> 表示响应体数据以流的形式返回 </td></tr></tbody></table>

    ```Java
    /*@Field，@FieldMap 为form表单形式的键值对*/
    /*需要添加@FormUrlEncoded表示表单提交 Content-Type:application/x-www-form-urlencoded*/
    @FormUrlEncoded
    @POST("update")
    Call<JsonObject> testField(@Field("name") String name, @Field("age") int age);

    /*@Part，@PartMap 用于POST文件上传*/
    /*其中@Part MultipartBody.Part代表文件，@Part("key") RequestBody或其它类型代表参数*/
    /*需要添加@Multipart表示支持文件上传的表单，Content-Type: multipart/form-data*/
    @Multipart
    @POST("upload")
    Call<JsonObject> testPart(@Part("desc") String desc, @Part MultipartBody.Part file);

    /*用于下载文件*/
    /*表示响应体数据以流的形式返回，如果没有使用该注解，默认会把数据全部载入内存，之后是从内存中读取数据，所以数据很大时，适合用该标记*/
    @Streaming
    @GET
    Call<ResponseBody> testStreaming(@Url String url);
    ```









