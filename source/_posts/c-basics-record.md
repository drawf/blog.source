---
layout: post
title: "C语言基础知识补习①"
categories: [tech]
date: 2017-02-18
tags: [C语言]
toc: true
description: 本篇是C语言的基础知识补习第一篇，共两篇，需要读者有一定的编程经验。
---

### 放在前边

> 注：本篇是C语言的基础知识补习第一篇，共两篇，需要读者有一定的编程经验。

[demo.c传送门](https://github.com/drawf/demo.retrofit)

### C/C++的开发工具

链接: [https://pan.baidu.com/s/1eSh1Uuu](https://pan.baidu.com/s/1eSh1Uuu) 密码: sp3t

CLion for Mac 2016.3 版本，CMake workflow、Modern C and C++、Semantic highlighting等新特性，
CLion 是一款由JetBrains出品的强大的C/C++开发工具，支持Windows、Mac、Linux平台，是现代的智能集成开发工具，支持各种功能，如代码自动完成、提醒、版本控制工具等，支持C、C++、 C++11 等标准，非常的不错！

### C语言相关

#### 预处理指令-*#include*

`#include <stdio.h>`意思是引用`stdio.h`头文件，在编译器实际编译之前，查找到该文件后把其中的所有内容复制到当前文件内并删除当前语句。

`#include <xxx.h>`用于标准库文件或系统提供的头文件，去保存系统标准头文件的位置查找头文件。

`#include "xxx.h"`用于用户自定义的头文件，先从当前目录查找是否有指定名称的头文件，找不到的话，再从标准文件目录中查找。

#### 基本数据类型

![](http://7sbl4z.com1.z0.glb.clouddn.com/blog/master/images/c_basic_type.jpg?imageMogr2/thumbnail/550x)

因为C语言不同类型变量的大小，是随着操作系统变化而变化的，所以使用 `sizeof(type)`方法得到对象或类型的存储字节大小。

```C
void testBasicType() {

    //字符类型 char
    char c = 'c';
    printf("c 的值：%c --> char 所占字节：%d\n", c, sizeof(char));
    //c 的值：c --> char 所占字节：1

    //短整形 short
    short sh = 32;
    printf("sh 的值：%d --> short 所占字节：%d\n", sh, sizeof(short));
    //sh 的值：32 --> short 所占字节：2

    //整形 int
    int i = 90;
    printf("i 的值：%d --> int 所占字节：%d\n", i, sizeof(int));
    //i 的值：90 --> int 所占字节：4

    //长整形 long
    long l = 12312;
    printf("l 的值：%ld --> long 所占字节：%d\n", l, sizeof(long));
    //l 的值：12312 --> long 所占字节：8

    //单精度浮点型 float
    float f = 12.3;
    printf("f 的值：%f --> float 所占字节：%d\n", f, sizeof(float));
    //f 的值：12.300000 --> float 所占字节：4

    //双精度浮点型 double
    double d = 234.3433;
    printf("d 的值：%lf --> double 所占字节：%d\n", d, sizeof(double));
    //d 的值：234.343300 --> double 所占字节：8

    printf("Hello %s\n", "World!");
    //Hello World!

    printf("无符号八进制：%o\n", 023);
    printf("有符号八进制：%#o\n", 023);
    //无符号八进制：23
    //有符号八进制：023

    printf("无符号十六进制：%x\n", 0x23443);
    printf("有符号十六进制：%#x\n", 0x23443);
    //无符号十六进制：23443
    //有符号十六进制：0x23443

    //关于printf()中的输出占位符：%c、%d等，其中%为输出格式描述符。

    //char --> %c
    //short、int --> %d ：d为digital（数字）
    //long --> %ld
    //float --> %f
    //double --> %lf

    //字符串 --> %s
    //无符号八进制 --> %o
    //有符号八进制 --> %#o
    //无符号十六进制 --> %x
    //有符号十六进制 --> %#x

}
```

#### 指针的概念

C语言中最重要的就是指针了，指针就是为了操作内存而生的。如同Java是对象的世界一样，C语言就是指针的世界。

> 指针也是变量，它存储的是变量的内存地址，也只能存储内存地址，直接赋值也会被转化成内存地址，它的强大之处就在于，它能通过内存地址去操作对应内存地址的内容。

```C
void testPointer() {

    int i = 50;//定义变量 i=50
    int *p;//声明 int 类型的指针变量 p，指针也是变量
    p = &i;//在指针变量 p 中存储 i 的地址，&i 是取变量 i 的地址

    printf("i 的地址：%#x\n", &i);
    printf("i 的地址：%#x\n", p);//p 变量中存的是 i 的地址
    printf("p 的地址：%#x\n", &p);
    printf("i 的值：%d\n", *p);//用一元运算符 * 来返回 p 变量存的地址的变量的值，即 i 的值
    //i 的地址：0x55d1674c
    //i 的地址：0x55d1674c
    //p 的地址：0x5e5d2740
    //i 的值：50

    /*指针变量就是用来操作内存空间的*/
    *p = 20;//通过 *p 我们操作 i 变量，给 i 变量赋值20
    printf("i 的值变成了：%d\n", *p);
    //i 的值变成了：20

}
```

#### 指针内存分析

![](http://7sbl4z.com1.z0.glb.clouddn.com/blog/master/images/c_pointer_memory.png)

```C
/*指针变量也是变量，所以指针也是可以运算的*/
void testPointerVariable() {
    int arr[] = {75, 40, 25, 80};

    printf("数组的地址：%#x\n", &arr);
    printf("这样得到数组的地址：%#x\n", arr);
    printf("第一个元素的地址：%#x\n", &arr[0]);
    printf("数组的值：%d\n", arr);
    printf("第一个元素的值：%d\n", *arr);
    //数组的地址：0x55b89730
    //这样得到数组的地址：0x55b89730
    //第一个元素的地址：0x55b89730
    //数组的值：1438160688
    //第一个元素的值：75

    /*0x55b89730 转为十进制为 1438160688，且*arr返回的是第一个元素的值，*/
    /*所以数组类型的变量，存储就是首个元素的地址*/

    printf("=============\n");

    for (int i = 0; i < 4; i++) {
        printf("索引方式取数组元素：%d\n", arr[i]);
    }

    printf("=============\n");

    int *pInt = arr;
    for (int j = 0; j < 4; j++) {
        printf("指针方式取数组元素：%d\n", *pInt);
        pInt++;
    }

    //索引方式取数组元素：75
    //索引方式取数组元素：40
    //索引方式取数组元素：25
    //索引方式取数组元素：80
    //=============
    //指针方式取数组元素：75
    //指针方式取数组元素：40
    //指针方式取数组元素：25
    //指针方式取数组元素：80

    /*通过指针变量 pInt 自增的方式，打印出了数组各元素的值，不难发现数组是*/
    /*用一段连续的内存空间存储数据，我们可以用同类型的指针，通过运算来直接操作内存*/
    /*注意：指针类型需要跟数组类型一致，在CLion编辑器下，两类型不一致时会报错，如：*/
    /*incompatible pointer types 'float *' and 'int[4]'*/

}
```

#### 函数

```C
/*
 * C语言的函数定义和Java的函数定义类似，只是没有 public ，private 等这样的权限控制
 *
 * 返回值类型 函数名(参数类型 参数名称, ...){
 *      函数体
 * }
 */
int add(int num1, int num2) {
    return num1 + num2;
}

/*
 * 通过传入指针类型可以在方法中修改参数原来的值
 */
void changeNumByPointer(int *pInt){
    printf("形参 pInt 的地址：%#x\n", pInt);
    *pInt = 90;
}

/*
 * 非指针类型的形参，会为 i 变量开辟新的内存空间，所以做不到修改参数原来的值
 */
void changeNum(int i){
    printf("形参 i 的地址：%#x\n", &i);
    i = 80;
}

void testChangeNum() {

    int i = 10;
    printf("i 原来的值：%d\n", i);
    printf("i 原来的地址：%#x\n", &i);
    //i 原来的值：10
    //i 原来的地址：0x50f548fc

    changeNumByPointer(&i);
    printf("i 现在的值：%d\n", i);
    //形参 pInt 的地址：0x50f548fc
    //i 现在的值：90

    changeNum(i);
    printf("i 现在的值：%d\n", i);
    //形参 i 的地址：0x50f548cc
    //i 现在的值：90
}
```

#### 二级指针和多级指针的概念

二级指针就是指针的指针，二级指针存储的是一级指针的内存地址。依次类推可以有三级、四级等等，统称为多级指针。

![](http://7sbl4z.com1.z0.glb.clouddn.com/blog/master/images/c_secondary_pointer_memory.png)

```C
void testSecondaryPointer() {

    int i = 10;
    int *pInt = &i;//一级地址存 i 的地址
    int **pInt1 = &pInt;//二级地址存 pInt 的地址

    printf("i 的地址：%#x\n", &i);
    printf("pInt 的地址：%#x\n", &pInt);
    printf("用二级指针取 pInt 的地址：%#x\n", pInt1);
    printf("用二级指针取 i 的地址：%#x\n", *pInt1);
    printf("用二级指针取 i 的值：%d\n", **pInt1);
    //i 的地址：0x56d688fc
    //pInt 的地址：0x56d688f0
    //用二级指针取 pInt 的地址：0x56d688f0
    //用二级指针取 i 的地址：0x56d688fc
    //用二级指针取 i 的值：10

    **pInt1 = 20;
    printf("用二级指针修改 i 的值：%d\n", i);
    //用二级指针修改 i 的值：20

}
```

#### 函数指针的概念

我们定义的函数跟变量一样，会有一个内存地址，我们可以把这个地址赋值给*函数指针*，这样通过函数指针就可以调用相应的函数。

```C
void logcat() {
    printf("随便打印一下..\n");
}

void testFuncPointer() {
    //函数指针定义：返回值类型 (函数指针)(函数参数) = 函数地址
    void (*pFunc)() = &logcat;

    pFunc();
    printf("函数的地址：%#x\n", logcat);
    printf("函数的地址：%#x\n", &logcat);
    printf("函数指针的值：%#x\n", pFunc);
    printf("函数指针的地址：%#x\n", &pFunc);
    //函数的地址：0xd5754c0
    //函数的地址：0xd5754c0
    //函数指针的值：0xd5754c0
    //函数指针的地址：0x5268b8f8
}
```

有了函数指针我们就可以将函数当作参数传入到其他函数中，这样就实现了其他高级语言里的*闭包*，看例子。

```Java
int add(int num1, int num2) {
    return num1 + num2;
}

int minus(int num1, int num2) {
    return num1 - num2;
}

/*接收 一个返回值为int类型，输入两个int类型参数的函数指针 和 两个int类型参数*/
void calculate(int(*pFunc)(int, int), int num1, int num2) {
    int i = pFunc(num1, num2);
    printf("计算完成：%d\n", i);
}

void testFuncPointer1() {

    calculate(&add, 2, 3);
    //计算完成：5
    calculate(&minus, 2, 3);
    //计算完成：-1

}
```
```Java
/*通过函数指针实现方法回调*/
void callback(char *msg) {
    printf("网络请求回调：%s\n", msg);
}

void requestNet(char *url, void(*pCallBack)(char *)) {
    printf("请求url：%s\n", url);
    sleep(2);//模拟网络耗时
    char *msg = "我是返回的数据";
    pCallBack(msg);

    //请求url：www.baidu.com
    //网络请求回调：我是返回的数据
}

void testFuncPointer2() {
    char *url = "www.baidu.com";
    requestNet(url, &callback);
}
```

#### 字符操作

```Java
/*字符数组*/
void testCharArray() {

    char arr[15] = {'a', 'b', ' ', 'd', 'e'};
    printf("字符数组：%s\n", arr);//用 %s 占位符可将字符数组作为字符串打印出来
    //字符数组：ab de

    arr[1] = 'Y';//修改第二个元素
    printf("修改了第二个字符：%s\n", arr);
    //修改了第二个字符：aY de

}
```

```Java
/*字符指针*/
void testCharPointer() {

    char *s = "Hello World!";//是一段连续的内存地址，不可修改
    printf("字符指针内存地址：%#x\n", s);//返回的是首个字符 H 的内存地址
    printf("打印字符串：%s\n", s);
    //字符指针内存地址：0x7ce4c63
    //打印字符串：Hello World!

    char *t = s + 4;//这样 t 就指向了首个 o 字符
    printf("o 字符的地址：%#x\n", t);
    //o 字符的地址：0x7ce4c67

    //截取字符串 llo World!
    s += 2;//s 指向了首个 l 字符
    while (*s) {
        printf("%c", *s);
        s++;
    }
    //llo World!

}
```

> 注：1，字符指针与字符数组最大的区别在于，字符指针不可修改内容，字符数组可以修改。2，输出格式占位符 %s 是把对应的参数*作为内存地址*，然后将地址指向的字符输出，并依次将该地址递增输出字符，直到遇到空字符时认为字符串结束。

接着再看几个字符操作的例子。

```Java

```

### 放在后边
如有疑问和建议欢迎留言。