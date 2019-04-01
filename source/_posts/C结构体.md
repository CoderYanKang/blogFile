---
layout: C
title: C结构体
date: 2017-02-07 12:19:47
categories: 
- C
tags: C
---

结构体 C中用户可自定义的数据类型，可以存放不同类型的值（数组用于存放相同的数据类型）。开发者可以根据实际情况创建一个新类型。

结构体可以包含结构体，在c++ 中结构体还可以包含函数，具有了类的部分功能。
<!--more-->
##定义

###打开或创建新文件

```
struct 结构体标识
{
		结构体成员列表	
}(结构体变量名)
```
三者至少出现2个
	
```
//结构体定义
struct Cars
{
   //车名
   char name[20];
   //排量
   float Tt;
   //马力
   int power;
};
	
void useStruct()
{
   //定义结构体变量
   struct Cars car1;
   struct Cars *car2;
	
   //结构体变量赋值
   strcpy(car1.name , "兰博基尼");
   car1.Tt = 2.0;
   car1.power = 300;
	
   LOGE("原车信息--车名：%s 排量： %1f 马力：%d" , car1.name , car1.Tt , car1.power);
   LOGE("我换车了，哈哈");
	
   car2 = &car1;
   strcpy(car2 -> name , "法拉利");
   car2 -> Tt = 2.0;
   car2 -> power = 500;
   LOGE("现车信息--车名：%s 排量： %f 马力：%d" , car2 -> name , car2 -> Tt , car2 ->power);
}
   E/native-dev(5376): 原车信息--车名：兰博基尼 排量： 2.000000 马力：300
   E/native-dev(5376): 我换车了，哈哈
   E/native-dev(5376): 现车信息--车名：法拉利 排量： 2.000000 马力：500
```

####结构体中的结构体
```

2
3
4
5
6
7
8
9
10
11
12
//此结构体的声明包含了指向自己类型的指针（链表）
struct HealthInfo
{
   char info[10];
};
	
struct Person
{
   char name[10];
   struct HealthInfo *healthinfo;
};

```

位域
略

   

  
      
	



