---
layout: post
title: "C语言基础知识补习②"
categories: [tech]
date: 2017-02-22
tags: [C语言]
toc: true
description: 本篇是C语言的基础知识补习的第二篇，共两篇，需要读者有一定的编程经验。
---

### 放在前边

> 注：本篇是C语言的基础知识补习的第二篇，共两篇，需要读者有一定的编程经验。

[demo.c传送门](https://github.com/drawf/demo.c)

### C/C++的开发工具

链接: [https://pan.baidu.com/s/1eSh1Uuu](https://pan.baidu.com/s/1eSh1Uuu) 密码: sp3t

CLion for Mac 2016.3 版本，CMake workflow、Modern C and C++、Semantic highlighting等新特性，
CLion 是一款由JetBrains出品的强大的C/C++开发工具，支持Windows、Mac、Linux平台，是现代的智能集成开发工具，支持各种功能，如代码自动完成、提醒、版本控制工具等，支持C、C++、 C++11 等标准，非常的不错！

### C语言相关

#### 结构体的概念

如同class（类）是Java中的结构化数据核心一样，struct（结构体）就是C语言中的结构化数据核心。

结构体中只有声明，函数不能实现，属性也不能初始化，需要在外部赋值，一般定义在头文件中。

```Java
/*定义一个Person结构体，关键字为struct*/
struct Person {
    char *name;
    int age;
};

struct News {
    char title[123];
    char *content;
};

/*简单使用*/
void testSimpleStruct() {

    struct Person person = {"张三", 25};
    printf("name:%s ==> age:%d\n", person.name, person.age);
    //name:张三 ==> age:25

    person.name = "Bob";
    person.age = 23;
    printf("name:%s ==> age:%d\n", person.name, person.age);
    //name:Bob ==> age:23

    struct News news;
    //news.title = "我是新闻标题"; 这是错误的赋值方法！报错为：array is not assignable
    //因为 news.title 是数组类型，"我是新闻标题"是 char * 类型
    strcpy(news.title, "我是新闻标题");//正确的赋值方法
    news.content = "我是内容";
    printf("title:%s ==> content:%s\n", news.title, news.content);
    //title:我是新闻标题 ==> content:我是内容

}
```

#### 结构体的其它定义形式

```Java
/*定义结构体时，同时定义一个或多个它的变量或指针。按照属性不能初始化原则，变量或指针都是没有值的*/
struct Product {
    char *name;
    char *desc;
} product, *pProduct, product1;

/*匿名结构体，可以做单例使用*/
struct {
    char *name;
    int age;
} *pStudent, student;

void testSimpleStruct2() {

    product.name = "苹果手机";
    product.desc = "就是贵";
    printf("name:%s ==> desc:%s\n", product.name, product.desc);
    //name:苹果手机 ==> desc:就是贵

    product1.name = "小米手机";
    product1.desc = "广告就是多";
    pProduct = &product1;
    /*通过指针操作属性，结构体指针使用 p->xx 操作符，相当于 (*p).xx 的简写*/
    printf("name:%s ==> desc:%s\n", (*pProduct).name, pProduct->desc);
    //name:小米手机 ==> desc:广告就是多

    pStudent = &student;
    (*pStudent).name = "张三";
    pStudent->age = 23;
    printf("name:%s ==> age:%d\n", student.name, student.age);
    //name:张三 ==> age:23

};
```

#### 结构体的嵌套

```Java
struct ProductBean {
    int total;
    int state;

    /*嵌套已定义好的结构体*/
    struct Product product;

    /*嵌套新声明的结构体*/
    struct Readme {
        char *content;
    } readme;
};

void testSimpleStruct3() {

    ProductBean pb = {10, 1, {"苹果手机", "就是贵"}, "售出不退"};
    printf("total:%d ==> state:%d ==> name:%s ==> desc:%s ==> content:%s\n",
           pb.total, pb.state, pb.product.name, pb.product.desc, pb.readme.content);
    //total:10 ==> state:1 ==> name:苹果手机 ==> desc:就是贵 ==> content:售出不退

    ProductBean pb1;
    ProductBean *p = &pb1;
    p->total = 2;
    p->state = 1;
    p->product.name = "小米手机";
    p->product.desc = "就是便宜";
    p->readme.content = "开机不会爆炸";
    printf("total:%d ==> state:%d ==> name:%s ==> desc:%s ==> content:%s\n",
           p->total, p->state, p->product.name, p->product.desc, p->readme.content);
    //total:2 ==> state:1 ==> name:小米手机 ==> desc:就是便宜 ==> content:开机不会爆炸

};
```

#### 结构体数组

```Java
void testStructArray() {

    Person person1 = {"张三", 21};
    Person person2 = {"李四", 33};

    Person persons[] = {person1, person2, {"赵六", 55}};

    //计算数组长度
    int len = sizeof(persons) / sizeof(Person);
    printf("数组长度：%d\n", len);
    //数组长度：3

    //通过指针遍历数组
    for (Person *pPerson = persons; pPerson < persons + len; pPerson++) {
        printf("name:%s ==> age:%d\n", pPerson->name, pPerson->age);
    }
    //name:张三 ==> age:21
    //name:李四 ==> age:33
    //name:赵六 ==> age:55

}
```

### 放在后边
如有疑问和建议欢迎留言。