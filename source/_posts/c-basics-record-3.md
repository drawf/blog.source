---
layout: post
title: "C语言基础知识补习③"
categories: [tech]
date: 2017-02-27
tags: [C语言]
toc: true
description: 本篇是C语言的基础知识补习的第三篇，共三篇，需要读者有一定的编程经验。
---

### 放在前边

> 注：本篇是C语言的基础知识补习的第三篇，共三篇，需要读者有一定的编程经验。

[demo.c传送门](https://github.com/drawf/demo.c)

### C/C++的开发工具

链接: [https://pan.baidu.com/s/1eSh1Uuu](https://pan.baidu.com/s/1eSh1Uuu) 密码: sp3t

CLion for Mac 2016.3 版本，CMake workflow、Modern C and C++、Semantic highlighting等新特性，
CLion 是一款由JetBrains出品的强大的C/C++开发工具，支持Windows、Mac、Linux平台，是现代的智能集成开发工具，支持各种功能，如代码自动完成、提醒、版本控制工具等，支持C、C++、 C++11 等标准，非常的不错！

### C语言相关

#### 类型别名

关键字为`typedef`，特点如下：

1. 不同别名代表在做不同的事情，如：typedef int jint
2. 不同的情况下，使用不同的别名
3. 书写简介

```Java
typedef char *Name;//简单别名

struct ImageInfo {
    char *name;
    int size;
    char *path;
};

typedef ImageInfo aImg;//结构体别名，只是个新名字，不是变量

void reImageName(ImageInfo *pImageInfo, char *msg) {
    pImageInfo->name = msg;
}

void testAlias() {
    Name a = "小李子";//Name 为 char* 类型
    printf("Name的值：%s\n", a);
    //Name的值：小李子

    aImg img = {"风景图", 122, "我是图片路径"};
    reImageName(&img, "美女图");
    printf("name:%s ==> size:%d ==> path:%s\n", img.name, img.size, img.path);
    //name:美女图 ==> size:122 ==> path:我是图片路径

}
```

#### 共用体（联合体）

不同类型的变量以相互覆盖的方式共用一段内存，始终是最后一个赋值的成员存在，有利于节省内存，共用体的大小为最大的成员的大小，关键字为`union`。

```Java
union mVal {
    char c;//1 byte
    short s;//2 byte
    int i;//4 byte
    long l;//8 byte
};

void testUnion() {

    mVal u;
    u.c = 'd';
    u.i = 10;//最后一次赋值有效

    printf("char:%c ==> int:%d ==> 大小：%d\n", u.c, u.i, sizeof(u));
    //char:
    //==> int:10 ==> 大小：8
    //u.c的值被损坏了，使用共同体的主要目的是，同一时间只使用一个变量

}
```

#### 枚举

```Java
/*枚举，列举所有的情况*/
enum NetStatus {
    NET_START,
    NET_LOADING,
    NET_SUCCESS,
    NET_ERROR
};

/*模拟网络请求*/
void httpRequest(char *url, void(*pCallback)(enum NetStatus, char *)) {

    printf("请求地址为：%s\n", url);
    //请求地址为：http://baidu.com

    pCallback(NET_START, "开始请求网络..");
    pCallback(NET_LOADING, "加载中..");
    sleep(2);
    pCallback(NET_SUCCESS, "请求成功");

}

void httpCallback(enum NetStatus status, char *result) {
    switch (status) {
        case NET_START:
            printf("onStart:%s\n", result);
            //onStart:开始请求网络..
            break;
        case NET_LOADING:
            printf("loading:%s\n", result);
            //loading:加载中..
            break;
        case NET_SUCCESS:
            printf("success:%s\n", result);
            //success:请求成功
            break;
        case NET_ERROR:
            printf("error:%s\n", result);
            break;
    }

}

void testEnum() {
    httpRequest("http://baidu.com", httpCallback);
}
```

#### 文件IO操作

在编写应用程序的时候，文件IO操作是不可避免的，小到日志本地化收集，大到数据格式化存储，都需要使用文件IO来操作文件流进行数据处理。

文件IO操作的一般步骤：

1. 创建一个File对象
2. 构建输入、输出流
3. 创建缓冲区，并读写数据
4. 关闭流

C语言中文件操作主要依靠*操作模式*来辨别是输入流还是输出流，mode：

1. r 打开只读文件，该文件必须存在。
2. r+ 打开可读写的文件，该文件必须存在。
3. w 打开只写文件，若文件存在则文件长度清为0，即该文件内容会消失，若文件不存在则建立该文件。
4. w+ 打开可读写文件，若文件存在则文件长度清为零，即该文件内容会消失，若文件不存在则建立该文件。
5. a 以追加的方式打开只写文件，若文件不存在，则会建立该文件，如果文件存在，写入的数据会被加到文件尾，即文件原先的内容会被保留。
6. a+ 以追加方式打开可读写的文件，若文件不存在，则会建立该文件，如果文件存在，写入的数据会被加到文件尾后，即文件原先的内容会被保留。

> 注：上述操作模式是针对文本文件的，如果要操作二进制文件，则需要在上述操作符后面加上 b，如rb、wb、ab等。

### 放在后边
如有疑问和建议欢迎留言。