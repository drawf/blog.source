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

### 放在后边
如有疑问和建议欢迎留言。