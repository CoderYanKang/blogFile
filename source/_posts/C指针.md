---
layout: C指针
title: C从忘记到捡起---指针
date: 2017-01-10 12:19:47
categories: 
- C
tags: C指针
---

指针一般指向一个函数或一个变量。在使用一个指针时，一个程序既可以直接使用这个指针所储存的内存地址，又可以使用这个地址里储存的函数的值
<!--more-->
##C语言基础–指针

简单例子

```
int v = 10;
int *p = &v;
printf("指针值：%d \n" , *p);
```
空指针

```
int *n = NULL;
printf("空指针： %x \n" , n);
//判断是否为空指针
if(n)
...
if(!n)
```

指针简单运算

```
int attr[5] = {1 , 2 , 3 , 4 , 5};
int *ap;
ap = attr;
for (int i = 0; i < 5; i ++){
 	printf("指针递增运算 %d \n" , *ap);
   ap ++;
}
int length = 5;
ap = &attr[length - 1];
for (int i = length -1 ; i >= 0 ; i --){ 	printf("指针递减运算: %d \n" , *ap);
	ap--;	
}
```

指针比较

```
while (ap <= &attr[length - 1])
{
	printf("指针比较运算：%d \n" , *ap);
	ap++;
}

```

数组指针

```
//数组指针
int* pointAttr[5];
for(int i = 0 ; i < 5 ; i ++){
pointAttr[i] = &attr[i];
}
for (int i = 0 ; i < 5; i++) {
printf("数组指针：%d \n" , *pointAttr[i]);
}
```

传递指针给函数

```
//给函数 传递指针参数
int* pointMethod(int* num1 , int* num2){
if (*num1 > *num2){
   return num1;
}
else{
   return num2;}
}
int a = 10;
int b = 20;	
int* max = pointMethod(&a, &b);
printf("最大值：%d \n" , *max);
```

多级指针

```
int mutilPoints(int* a , int* b){
		int** aa = &a;
		int** bb = &b;		
		return **aa + **bb;
}
```

函数指针

```
// 声明函数指针 变量
int* (*myPoint)(int * a , int *b);
int main(int argc, const char * argv[])
	
//使用
myPoint = &mp;
int a1 = 20;
int b1 = 10;
int* c = (*myPoint)(&a1, &b1);
printf("函数指针：%d \n" , *c);
//函数
int* mp(int* a , int* b)
{
	int c = *a - *b;
	return &c;
}
```

指针函数

```
( ( void( )() )0 )()
x = (void( )())0 //强转 为指针函数
( x )() //调用函数
```

函数指针数组

```
int* (*mp)(int* a , int * b)
int* (*mp[2](int* a , int * b))
//方法
int* f1(int *a , int* b){...}
int* f2(int *a , int* b){...}	
mp[0] = &f1;
mp[1] = &f2;
	
(*mp[0])(&a , &b);
(*mp[1])(&a , &b);
//函数指针数组的指针
char * (*a[3])(char * p);
char * (*(*pf)[3])(char * p);
pf = &a;
	
a[0] = fun1;  //
a[1] = &fun2; //效果同上
a[2] = &fun3;
	
pf[0][0]("fun1");
pf[0][1]("fun2");
pf[0][2]("fun3");

```





   

  
      
	



