---
layout: C
title: C文件操作
date: 2017-02-07 12:19:47
categories: 
- C
tags: C
---

C文件读写操作，是C语言在应用中一个很重要的知识点。特别是在多媒体（图片、音视频）处理中应用的非常多。
<!--more-->
##C文件操作

###打开或创建新文件

	FILE *fopen( const char * filename, const char * mode );
> 参数： filename文件名 ， mode打开模式
	
####普通文件

	r        只读方式打开一个已有文件，文件必须存在
		 
	w        只写方式打开一个文件，不存在会创建，存在则清空
		 
	a        以追加的方式写文件，不存在会创建，存在会追加
		 
	r+       以读写的方式打开，文件不存 不会创建
		 
	w+       以读写的方式打开，文件不存在会创建 ， 存在会清空原内容
		 
	a+       以追加方式读写文件, 文件不存在会创建新的文件, 文件存在会)
	
>二进制文件
>
需要在mode 后面添加b
"rb", "wb", "ab", "rb+", "r+b", "wb+", "w+b", "ab+", "a+b"

####读写文件
字符读写函数: fgetc 和 fputc 只能处理文本文件，不能处理二进制文件

fgetc函数默认指向文件第一个位置。

```
const char* filename = "CPP_01/aaa.txt";
FILE* fp = fopen(filename, "w+");
while( ch != EOF) //EOF为文件结束
{
	ch = fgetc(fp);
	// putchar 把ch显示在屏幕上
	putchar(ch); 
}
	
fclose(fp);

```

fputc函数 向文件写入一个字符

```
const char* filename = "CPP_01/aaa.txt";
FILE* fp = fopen(filename, "w+");
ch = getchar();
while( ch != EOF) //EOF为文件结束
{
	fputc(ch, fp); 
	//fputc(ch, fp); 函数功能： 将字符（ch的值）输出到fp所指向的文件中去
	ch = getchar();
}
fclose(fp);

```


####fprintf 和 fscanf 函数

```
int	 fscanf(FILE *, const char *, ...);
int	 fprintf(FILE *, const char *, ...);

```
> FILE stream：文件指针
char format：格式字符串
[argument…]：输入列表

####fprintf 函数 写入

```
int i = 10;
double fp = 1.5;
char s[] = "this is a string";
char c = '\n';
stream = fopen("fprintf.out", "w");
fprintf(stream, "%s%c", s, c);
fprintf(stream, "%d\n", i);
fprintf(stream, "%f\n", fp);
fclose(stream);
```

####fscanf 函数 读取

```
long l;
float fp;
char s[81];
char c;
stream = fopen("fscanf.out", "w+");
if(stream==NULL)
printf("The file fscanf.out was not opened\n");
else
{
    //写入数据
    fprintf(stream,"%s%ld%f%c","a-string", 65000,3.14159, 'x');
    //将指针设置至文件开头
    fseek(stream,0L,SEEK_SET);
    //从文件中读取数据
    fscanf(stream,"%s",s);
    fscanf(stream,"%ld",&l);
    fscanf(stream,"%f",&fp);
    fscanf(stream,"%c",&c);
    //输出读取的数据
    printf("%s\n",s);
    printf("%ld\n",l);
    printf("%f\n",fp);
    printf("%c\n",c);
    fclose(stream)
    
}
```

fwrite与fread 二进制文件读写

> size_t	 fwrite(const void *, size_t, size_t, FILE *);
size_t	 fread(void *, size_t, size_t, FILE *);


###返回值：返回实际写入的数据块数目

1、buffer：是一个指针，对fwrite来说，是要写入的数据的地址； 对于fread 是要接收读取数据的指针

2、size：要写入(读取)内容的单字节数；

3、count:要进行写入(读取)size字节的数据项的个数；size * count 写入的字节数

4、stream:目标文件指针；

5、返回实际写入的数据项个数count。

fwrite 字节写入

```
FILE * pFile;
char buffer[] = { 'x' , 'y' , 'z' };
pFile = fopen ( "myfile.bin" , "wb" );
fwrite (buffer , sizeof(buffer), 1 , pFile );
fclose (pFile);
```

fread 字节读取

```
const char* file = "CPP_01/aaa.txt";
FILE* fp = fopen(file, "rb"); // 模式为读取
if (fp == NULL)
{
	printf("failed to read file \n");
	return -1;
}  
	
char buf[2];	
//读取文件
while (!feof(fp))
{
	long len = fread(buf, 1, 2, fp);
	printf("读取的长度 %ld \n" , len);
	printf("%s" , buf);
}
```

####关闭文件

      int fclose( FILE *fp );
      
关闭文件成功返回0，失败返回EOF

####Fseek 指定位置读写

    int fseek(FILE *stream, long offset, int fromwhere);
    
>参数：offset 偏移量 frowwhere 偏移位置 
偏移起始位置：文件头0(SEEK_SET)，当前位置1(SEEK_CUR)，文件尾2(SEEK_END)

```
// c 文件写入
const char* filename = "CPP_01/aaa.txt";
FILE* fp = fopen(filename, "wb");
if (fp == NULL)
{
	printf("failed to open file\n");
	return -1;
}
char buf[] = "hello file";
// 写入字符
fwrite(buf, 1, sizeof(buf), fp);
	    
// 写入数字
double num = 123.55;
fwrite(&num, 1, sizeof(num), fp);
	    	    
//写入字符串
string text = "\nwrite text";
fwrite(&text, 1,text.length(), fp);
	    
// 关闭文件
fclose(fp);
	    
//读取文件
const char* file = "CPP_01/aaa.txt";
FILE* fp = fopen(file, "rb"); // 模式为读取
if (fp == NULL)
{
	 printf("failed to read file \n");
	 return -1;
}
	    
char buf[2];
//从指定文件位置开始读取
fseek(fp, 2, SEEK_SET);
// fseek(fp, -2, SEEK_END);
	
//读取文件
while (!feof(fp))
{
	 long len = fread(buf, 1, 2, fp);
	 printf("读取的长度 %ld \n" , len);
	 printf("%s" , buf);
}
	    
//按行写入
char txt[20] = "按行写入";
double num = 123;
	    
const char* filename = "CPP_01/aaa.txt";
FILE* fp = fopen(filename, "wb");
	    
fprintf(fp, "%s\n" , txt);
fprintf(fp, "%f\n" , num);
```


   

  
      
	



