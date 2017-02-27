---
layout: post
title: "C语言基础知识补习②"
categories: [tech]
date: 2017-02-22
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

#### 动态内存分配

*动态内存分配*：在程序运行过程中，动态指定需要使用的内存大小，并手动释放，释放之后这些内存还可以被重新使用
*静态内存分配*：分配的内存大小是固定的，存在问题：1.很容易超出栈内存的最大值 2.为了防止内存不够用会开辟更多的内存，容易浪费内存

C/C++编译的程序占用的内存分配表

| 内存 | 描述 | 特性 |
| :--: | :--: | :--: |
| 栈区（stack） | windows下，栈内存分配2M（确定的常数），超出了限制，提示stack overflow错误 | 编译器自动分配释放，主要存放函数的参数值，局部变量值等 |
| 堆区（heap） | 用于动态内存分配 | 程序员手动分配释放，可占操作系统80%内存 |
| 全局区或静态区 | 存放全局变量和静态变量；程序结束时由系统释放，分为全局初始化区和全局未初始化区 | 只初始化一次 |
| 字符常量区 | 常量字符串放于此 | 程序结束时由系统释放 |
| 程序代码区 | 存放函数体的二进制代码 | 该区指令中包括操作码和要操作的对象（或对象地址引用） |

```Java
int a=0;//全局初始化区
char *p;//全局未初始化区

void main(){

   int b;          //栈
   char c[]="bb";  //栈
   char *p1;       //栈
   char *p2="123"; //其中，"123"在常量区，p3在栈区
   static int c=0; //全局区

   p1=(char*)malloc(10);   //10个字节区域在堆区
   strcpy(p1,"123");    //"123"在常量区，编译器可能会优化为和p2的指向同一块区域

}
```

`malloc()`函数用来动态地申请连续的内存空间，`realloc()`函数用来重新分配内存

```Java
void testMalloc() {
    //静态内存分配创建数组，数组大小是固定的
    //int i = 10;
    //int a[i];

    int len = 5;
    printf("数组的长度：%d\n", len);
    //数组的长度：5

    //开辟内存，大小为len*4字节，p是数组的首地址，p就是数组的名称
    int *p = (int *) malloc(len * sizeof(int));

    //给数组元素赋值（使用这一块刚刚开辟出来的连续的内存空间）
    for (int i = 0; i < len; i++) {
        p[i] = rand() % 100;
        printf("数组元素值为：%d ==> 地址为：%#x\n", p[i], &p[i]);
    }
    //数组元素值为：7 ==> 地址为：0x8ec02850
    //数组元素值为：49 ==> 地址为：0x8ec02854
    //数组元素值为：73 ==> 地址为：0x8ec02858
    //数组元素值为：58 ==> 地址为：0x8ec0285c
    //数组元素值为：30 ==> 地址为：0x8ec02860

    int addLen = 5;
    printf("数组增加的长度：%d\n", addLen);
    //数组的长度：5

    //新内存大小为(len+addLen)*4字节
    int *p1 = (int *) realloc(p, (len + addLen) * sizeof(int));
    if (p1 == NULL) {
        printf("内存重新分配失败");
    } else {
        for (int i = 0; i < len + addLen; i++) {
            p1[i] = rand() % 100;
            printf("数组元素值为：%d ==> 地址为：%#x\n", p1[i], &p1[i]);
        }
    }
    //数组元素值为：72 ==> 地址为：0x8ec02850
    //数组元素值为：44 ==> 地址为：0x8ec02854
    //数组元素值为：78 ==> 地址为：0x8ec02858
    //数组元素值为：23 ==> 地址为：0x8ec0285c
    //数组元素值为：9 ==> 地址为：0x8ec02860
    //数组元素值为：40 ==> 地址为：0x8ec02864
    //数组元素值为：65 ==> 地址为：0x8ec02868
    //数组元素值为：92 ==> 地址为：0x8ec0286c
    //数组元素值为：42 ==> 地址为：0x8ec02870
    //数组元素值为：87 ==> 地址为：0x8ec02874

    //手动释放内存，free()释放动态分配的内存空间
    if (p != NULL) {
        free(p);
        p = NULL;
    }

    if (p1 != NULL) {
        free(p1);
        p1 = NULL;
    }

}
```

`realloc()`重新分配内存的特点：

1. 如果是缩小内存，缩小的那部分数据会丢失，扩大时内存依旧是连续的
2. 如果当前内存段后的内存空间够用，会直接扩展这段内存空间，返回原指针
3. 如果当前内存段后的空闲内存不够用，就会使用堆中的第一个能够满足这一要求的内存块，将目前的数据复制到新的位置，并将原来的数据释放掉，返回新的内存地址
4. 如果申请失败，返回NULL，原来的指针仍然有效

内存分配需要注意的几点：

1. 不能多次释放
2. 释放完之后（指针仍然有值），给指针置NULL，标志释放完成
3. 内存泄露（p需要在赋值之前释放前一个内存空间）

#### 结构体动态内存分配

```Java
void testStructMalloc() {

    Person *person = (Person *) malloc(sizeof(Person) * 5);
    Person *p = person;
    p->name = "小明";
    p->age = 17;
    p++;
    p->name = "小李";
    p->age = 22;
    p++;
    p->name = "小王吧";
    p->age = 13;

    for (Person *i = person; i < person + 3; i++) {
        printf("name:%s ==> age:%d\n", i->name, i->age);
    }
    //name:小明 ==> age:17
    //name:小李 ==> age:22
    //name:小王吧 ==> age:13

}
```

#### 结构体与函数指针

```Java
struct Dog {
    char *name;
    int age;

    //结构体中不能有函数实体
    void (*wow)(char *);
};

void dogWow(char *msg) {
    printf("dog 说:%s\n", msg);
}

void testDogWow() {

    Dog dog = {"吉娃娃", 2, dogWow};//将函数名称赋值给函数指针
    dog.wow("汪汪！");
    printf("函数的地址：%#x\n", dogWow);
    //dog 说:汪汪！
    //函数的地址：0xcf56d20

}
```

### 放在后边
如有疑问和建议欢迎留言。