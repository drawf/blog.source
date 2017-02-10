---
layout: post
title: "结合Retrofit2.0体验OkHttp+RxJava"
categories: [tech]
date: 2017-02-8
tags: [OkHttp,RxJava]
toc: true
description: 本篇是结合Retrofit2.0来使用RxJava、OkHttp的一些姿势，关于RxJava的基础知识请先行补充。
---

### 放在前边
> 注：本篇是结合Retrofit2.0来使用RxJava、OkHttp的一些姿势，关于RxJava的基础知识请先行补充。

[demo.retrofit传送门](https://github.com/drawf/demo.retrofit)

![](http://7sbl4z.com1.z0.glb.clouddn.com/demo.retrofit/blog/master/images/screenshot_2.png?imageMogr2/thumbnail/250x)

### OkHttp相关

#### Interceptors概述

Interceptors are a powerful mechanism that can monitor, rewrite, and retry calls.

拦截器是一种强大的机制，可以监视、重写和重试请求.

拦截器分两种：

1. Application Interceptors 应用拦截器，通过`OkHttpClient.Builder()`调用`addInterceptor()`方法来注册。
2. NetWork Interceptors 网络拦截器，通过`OkHttpClient.Builder()`调用`addNetWorkInterceptor()`方法来注册。

通过一张图看一下二者的区别：

![](http://upload-images.jianshu.io/upload_images/1504154-8daf5fd9540545d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)

应用拦截器只关注程序从发出请求到拿到结果，而不关心网络底层是从缓存取的结果还是从一次真正的网络请求拿到的结果。

网络拦截器只关注每一次真正的网络请求。

所以，当一次请求发生一次重定向时，应用拦截器执行一次，网络拦截器会执行两次；当一次请求读的缓存时，应用拦截器执行一次，网络拦截器并不会执行。

#### 自定义LoggingInterceptor

[LoggingInterceptor传送门](https://github.com/drawf/demo.retrofit/blob/master/app/src/main/java/tv/wanzi/demo/retrofit/interceptor/LoggingInterceptor.java)

首先看`Interceptor`接口，覆写`intercept`方法，拦截的核心代码都在该方法中写。

```Java
public interface Interceptor {
    /*需要覆写的拦截方法，具体操作都在该方法中进行*/
    Response intercept(Chain chain) throws IOException;

    /*通过Chain实例，可以得到Request实例，并可以通过proceed方法得到Response实例*/
    interface Chain {
        Request request();

        /*该方法每次调用都是进行一次网络请求（可能是取的缓存可能是真实网络请求），所以调用一次即可，切勿盲目多次调用*/
        Response proceed(Request request) throws IOException;

        Connection connection();
    }
}
```

再看一下`LoggingInterceptor`的核心代码：

```Java
@Override
public Response intercept(Chain chain) throws IOException {
    /*得到原来的Request实例后就可以 重写请求*/
    Request originRequest = chain.request();

    long t1 = System.nanoTime();
    /*得到原来的Response实例后就可以 重写响应*/
    Response originResponse = chain.proceed(originRequest);
    long t2 = System.nanoTime();

    if (BuildConfig.DEBUG && eLog != LOG.NONE) {
        double time = (t2 - t1) / 1e6d;

        //url ⇢ http://xxx
        if (eLog == LOG.DEFAULT) {
            String format = "method%s%s\nurl%s%s\nbody%s%s\ntime%s%s\n" +
                    "response code%s%s\nresponse body%s%s\n";

            String message = String.format(format,
                    SEPARATOR, originRequest.method(),
                    SEPARATOR, originRequest.url(),
                    SEPARATOR, requestBody2String(originRequest.body()),
                    SEPARATOR, time,
                    SEPARATOR, originResponse.code(),
                   /*这里需要注意的是originResponse.body()一旦消费掉后，响应体就会为空，解决方法见responseBody2String源码*/
                    SEPARATOR, responseBody2String(originResponse.body()));
            LogUtils.v(message);

        } else if (eLog == LOG.ALL) {
            String format = "method%s%s\nurl%s%s\nheaders%s%s\nbody%s%s\ntime%s%s\n" +
                    "response code%s%s\nresponse headers%s%s\nresponse body%s%s\n";

            String message = String.format(format,
                    SEPARATOR, originRequest.method(),
                    SEPARATOR, originRequest.url(),
                    SEPARATOR, originRequest.headers(),
                    SEPARATOR, requestBody2String(originRequest.body()),
                    SEPARATOR, time,
                    SEPARATOR, originResponse.code(),
                    SEPARATOR, originResponse.headers(),
                    SEPARATOR, responseBody2String(originResponse.body()));
            LogUtils.v(message);
        }

    }
    return originResponse;
}

private static String responseBody2String(ResponseBody responseBody) throws IOException {
    if (responseBody == null) return "this request has no response body.";

    StringBuilder sb = new StringBuilder();

    BufferedSource source = responseBody.source();
    source.request(Long.MAX_VALUE); // Buffer the entire body.
    Buffer buffer = source.buffer().clone();//clone出响应体内容，这样就不会消费掉了

    Charset charset = Charset.forName("UTF-8");
    MediaType contentType = responseBody.contentType();
    if (contentType != null) {
        charset = contentType.charset(charset);
    }

    if (isPlaintext(buffer)) {
        sb.append(buffer.readString(charset));
    } else {
        sb.append(responseBody.contentLength()).append("-byte binary body omitted)");
    }
    return sb.toString();
}
```

#### OkHttp缓存概述

OkHttp是由Square发布的一个HTTP client，它支持高速缓存服务器响应的语义，即Header中的`Cache-control`，`only-if-cached`等标签。

OkHttp的缓存设计和浏览器的一样，缓存是自动完成的，完全由服务器的Header决定，是用来提升用户体验降低服务器负荷的。

#### 一点HTTP缓存基础知识

> 注：图中的 ETag 和 Last-Modified 没有优先级。

![](http://upload-images.jianshu.io/upload_images/98641-4bd320d4e34af60a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

1. **Expires**

    HTTP1.0使用的过期策略，它使用时间戳来标识缓存是否过期。这个方式缺陷很明显，客户端和服务端的时间不同步时，导致过期判断经常不准确。现在HTTP请求基本都使用HTTP1.1以上了，这个字段基本没用了。

    ```Java
    Expires: Thu, 12 Jan 2017 11:01:33 GMT
    ```

2. **Cache-Control**

    Cache-Control与Expires的作用一致，区别在于前者使用过期时间长度来标识是否过期，例如前者使用过期为30天，后者使用过期时间为2017年2月30日。因此使用Cache-Control能够较为准确的判断缓存是否过期，现在基本上都是使用这个参数。

    ```Java
    /*表示此次请求结果过期时长为60s*/
    Cache-control: max-age=60
    ```

3. **条件GET请求(Conditional GET Requests)与304**

    如果缓存过期或者强制放弃缓存，在此情况下，缓存策略全部交给服务器判断，客户端只发送条件get请求即可，如果缓存是有效的，则返回304 Not Modified，否则直接返回body。

    ```Java
    第一种方式：Last-Modified-Date

    /*客户端第一次请求时服务器返回如下*/
    Last-Modified: Tue, 12 Jan 2016 09:31:27 GMT

    /*客户端再次请求时，通过发送如下，交给服务器进行判断，如果缓存可用便返回304*/
    If-Modified-Since: Tue, 12 Jan 2016 09:31:27 GMT

    第二种方式：ETag，ETag是对资源文件的一种摘要，客户端并不需要了解实现细节。

    /*当客户端第一请求时，服务器返回如下*/
    ETag: "5694c7ef-24dc"

    /*客户端再次请求时，通过发送如下，交给服务器进行判断，如果缓存可用便返回304*/
    If-None-Match:"5694c7ef-24dc"
    ```

#### 一点Android存储访问目录知识

1. **应用数据目录（$appDataDir）**

    内部存储：$appDataDir = $rootDir/data/$packageName

    外部存储：$appDataDir = $rootDir/Android/data/$packageName

    app卸载之后，这两个目录下的数据会被系统删除，我们应将应用的数据放在这两个目录下。

    ```Java
    /*使用 外部存储 需要的权限，从API 19/Andorid 4.4/KITKAT开始，不再需要显式声明这两个权限，除非要读写其他应用的应用数据($appDataDir)*/
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    ```

2. **应用数据目录下的-*缓存目录***

    内部存储：Context.getCacheDir()，机身内存不足时，文件就会被删除。

    外部存储：Context.getExternalCacheDir()，外部存储没有实时监控，空间不足时，文件不会被实时删除，可能返回空对象。

    ```Java
    Context.getCacheDir()
    /data/data/tv.wanzi.demo.retrofit/cache

    Context.getExternalCacheDir():
    /storage/sdcard0/Android/data/tv.wanzi.demo.retrofit/cache
    ```

3. **应用数据目录下的-*文件目录***

    内部存储：Context.getFilesDir()，Context.getFileStreamPath(String name) 返回以 name 为文件名的文件对象，name 为空时返回 $filesDir 本身。

    ```Java
    Context.getFilesDir()
    /data/data/tv.wanzi.demo.retrofit/files

    Context.getFileStreamPath("")
    /data/data/tv.wanzi.demo.retrofit/files

    Context.getFileStreamPath("file1"):
    /data/data/tv.wanzi.demo.retrofit/files/file1
    ```

    外部存储：Context.getExternalFilesDir(String type)，type 为空时返回 $filesDir 本身。

    ```Java
    /*type 系统指定了几种*/
    Environment.DIRECTORY_MUSIC
    Environment.DIRECTORY_PICTURES
    Environment.DIRECTORY_MOVIES
    Environment.DIRECTORY_DOWNLOADS
    ...

    Context.getExternalFilesDir()
    /storage/sdcard0/Android/data/tv.wanzi.demo.retrofit/files

    Context.getExternalFilesDir(Environment.DIRECTORY_MUSIC)
    /storage/sdcard0/Android/data/tv.wanzi.demo.retrofit/files/Music

    Context.getExternalFilesDir("responses")
    /storage/sdcard0/Android/data/tv.wanzi.demo.retrofit/files/responses
    ```

4. **$cacheDir/$filesDir的安全性**

    内部存储：$cacheDir，$filesDir是app安全的，其他应用无法读取本应用的数据。

    外部存储：这两个文件夹其他应用程序也可访问，$filesDir中的媒体文件，不会被当做媒体扫描出来，加到媒体库中。

#### 使用OkHttp的缓存功能

```Java
OkHttpClient client = new OkHttpClient.Builder()
/*配置好缓存即可使用缓存功能*/
.cache(FileUtils.getOkHttpCache())
.build();

public static Cache getOkHttpCache() {
    File responses = getEFDDir("responses");
    if (responses == null) return null;
    /*配置缓存目录及缓存大小*/
    return new Cache(responses, HTTP_RESPONSE_DISK_CACHE_MAX_SIZE);//10 * 1024 * 1024=10M
}

/*获取外部存储目录*/
private static File getEFDDir(String name) {
    if (!hasSDCardMounted()) return null;

    Context context = MainApplication.getContext();
    return context.getExternalFilesDir(name);
}
```







### 放在后边
关于Retrofit2.0的姿势就写到这里了，如有疑问和建议欢迎留言。