---
layout: C
title: C动态内存管理
date: 2017-02-07 12:19:47
categories: 
- C
tags: C
---

动态内存管理，即是在我们需要使用内存是进行动态分配所需要的存储空间，并在不需要时进行回收（内存泄露）的方法
<!--more-->
##C动态内存管理

### 背景

C/C++ 定义的4个内存区间

1、代码区

2、全局变量与静态变量区

3、局部变量区（栈区）

4、动态存储区或自由存储区（堆区）

在实际开发中，当我们使用的变量的大小需要即时分配大小的时候。静态存储分配方式就显然无法满足需求。编译时我们无法预定存储空间的大小。

###特点：

1、不需要预先分配存储空间。

2、分配的空间可以根据实际需求进行改变。

###重要函数

1、内存分配函数 

```
extern __mallocfunc void*  calloc(size_t, size_t);

extern void *malloc(unsigned int num_bytes)

```

2、C修改分配的内存

```
extern void*  realloc(void *, size_t)
```

3、C释放分配的内存

```
extern void  free(void *)
```

>malloc 与 calloc的区别：calloc在动态分配完内存后，自动初始化该内存空间为0，而malloc不会初始化，存储的是随机数据。

```
int * dynamicintArray = (int*)malloc(sizeof(int) * 5);
//判断是否分配了足够的内存
if(dynamicintArray == NULL)
{
	LOGE("molloc error.....");
}
else
{
    //初始值
    for(int i = 0 ; i < 5 ; i++)
    {
    	LOGE("初始值：%d" , dynamicintArray[i]);
    }
    
    for(int i = 0 ; i < 5 ; i++)
    {
    	dynamicintArray[i] = i;
    }
    
    for(int i = 0 ; i < 5 ; i++)
    {
    	LOGE("value：%d" , dynamicintArray[i]);
    }
}
//修改原有分配的内存大小 扩大或者缩小
int* newdynamicintArray = (int*)realloc(dynamicintArray , sizeof(int) * 10);
if(newdynamicintArray == NULL)
{
	LOGE("realloc error.....");
}
else
{
	//改变分配空间
	for(int i = 0 ; i < 10 ; i++)
	{
		LOGE("改变分配空间：%d" , dynamicintArray[i]);
	}
}	
//释放的内存
free(dynamicintArray);

E/native-dev(2196): 初始值：3
E/native-dev(2196): 初始值：1
E/native-dev(2196): 初始值：1600613985
E/native-dev(2196): 初始值：829121646
E/native-dev(2196): 初始值：1801735775
E/native-dev(2196): value：0
E/native-dev(2196): value：1
E/native-dev(2196): value：2
E/native-dev(2196): value：3
E/native-dev(2196): value：4
E/native-dev(2196): 改变分配空间：0
E/native-dev(2196): 改变分配空间：1
E/native-dev(2196): 改变分配空间：2
E/native-dev(2196): 改变分配空间：3
E/native-dev(2196): 改变分配空间：4
E/native-dev(2196): 改变分配空间：1685419091
E/native-dev(2196): 改变分配空间：1970536569
E/native-dev(2196): 改变分配空间：6256996
E/native-dev(2196): 改变分配空间：-292087456
E/native-dev(2196): 改变分配空间：-375520288

```

>使用 calloc 初始值为 0


```
E/native-dev(2196): 初始值：0
E/native-dev(2196): 初始值：0
E/native-dev(2196): 初始值：0
E/native-dev(2196): 初始值：0
E/native-dev(2196): 初始值：0
E/native-dev(2196): value：0
E/native-dev(2196): value：1
E/native-dev(2196): value：2
E/native-dev(2196): value：3
E/native-dev(2196): value：4

```

###memset函数

	void memset(void s, int ch, size_t n);
  

>将s中当前位置后面的n个字节 （typedef unsigned int size_t ）用 ch 替换并返回 s 。
快速初始化


	char buffer[4];
	memset(buffer,0,sizeof(char)*4);
	strcpy(buffer,"abc");
	//"abc"中最后隐藏的'\0'占一位，总长4位。

     
###sizeof 函数

>sizeof是何方神圣？？sizeof乃C/C++中的一个操作符（operator）是也，简单的说其作
用就是返回一个对象或者类型所占的内存字节数。

语法：
	sizeof有三种语法形式，如下：
	
	1) sizeof( object );
	
	sizeof( 对象 );
	
	2) sizeof( type_name );
	
	sizeof( 类型 );
	
	3) sizeof object;
	sizeof 对象;	


###C各基本类型所占字节数

```
16位编译
char ：1个字节
char*(即指针变量): 2个字节
short int : 2个字节
int：  2个字节
unsigned int : 2个字节
float:  4个字节
double:   8个字节
long:   4个字节
long long:  8个字节
unsigned long:  4个字节
	
32位编译器
	
char ：1个字节
char*（即指针变量）: 4个字节（32位的寻址空间是2^32, 即32个bit，也就是4个字节。同理	64位编译器）
short int : 2个字节
int：  4个字节
unsigned int : 4个字节
float:  4个字节
double:   8个字节
long:   4个字节
long long:  8个字节
unsigned long:  4个字节
	
64位编译器
	
char ：1个字节
char*(即指针变量): 8个字节
short int : 2个字节
int：  4个字节
unsigned int : 4个字节
float:  4个字节
double:   8个字节
long:   8个字节
long long:  8个字节
unsigned long:  8个字节
```

