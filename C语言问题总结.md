# C语言语法
## 文件操作
### 格式：
```c
FILE *in;
in=fopen(文件名,使用文件方式)
```
### 使用文件方式（常见）：
   * r(只读):为了输入数据，打开一个存在的文本文件，如果打开文件不存在会出错
   * w(只写):为了输入数据，打开一个文本文件，如果打开文件不存在则会建立一个新文件
   * wb(只写):为了输入数据，打开一个二进制文件，如果打开文件不存在则会建立一个新文件
   * rb(只读):为了输入数据，打开一个二进制文件，如果打开文件不存在会出错
   * __注：处理除开文本文件的其他格式的文件需要用wb/rb__
### 判断是否到文件尾的两种写法
   * 错误写法
```c
char c;
while(!feof(fp))
{
c = fgetc(fp);
printf("%X/n", c); 
}
```
   * 会发现多输出了一个FF，原因就是在读完最后一个字符后，fp->flag仍然没有被置为_IOEOF，而feof()仍然没有探测到文件结尾。
   * 再次调用fgetc()执行读操作，feof()才能探测到文件结尾。这样就多输出了一个-1(即FF)。
    
   * 正确写法
   ```c
   char c;
c = fgetc(fp);
while(!feof(fp))
{
printf("%X/n", c); 
c = fgetc(fp);
} 
   ```
   * 参考链接：[链接](https://blog.csdn.net/woaisia/article/details/46441449?utm_source=blogxgwz1)
----
## 关于C语言控制台编程（int main(int argc,char *argv[])
### 参数含义解析
   * argc是命令行总的参数个数argv[]是argc个参数，其中第0个参数是程序的全名，以后的参数命令行后面跟的用户输入的参数
   * char* argv[]是一个指针数组，即数组的每个元素为一个指针，存储一个地址
   * argc与argv解释 [链接](https://blog.csdn.net/hopeneversleep/article/details/55798722)
   > 指针数组与数组指针的区别：
   >> 数组指针（也称行指针）。定义 int (*p)[n];()优先级高，首先说明p是一个指针，指向一个整型的一维数组，这个一维数组的长度是n，也可以说是p的步长。也就是说执行p+1时，p要跨过n个整型数据的长度
   >> 指针数组：定义 int *p[n];[]优先级高，先与p结合成为一个数组，再由int*说明这是一个整型指针数组，它有n个指针类型的数组元素。这里执行p+1时，则p指向下一个数组元素，这样赋值是错误的：p=a；因为p是个不可知的表示，只存在p[0]、p[1]、p[2]...p[n-1],而且它们分别是指针变量可以用来存放变量地址。但可以这样 *p=a; 这里*p表示指针数组第一个元素的值，a的首地址的值。
   * 指针数组与数组指针区别 [链接](https://www.cnblogs.com/mq0036/p/3382732.html)
### 在命令行运行时注意事项
   * __只能在VS2017中进行生成解决方案，而不能直接运行（假设代码是在VS2017 IDE编写的）__
   * __且命令行指的是cmd打开的窗口，运行格式为:源文件名.exe 参数1 参数2 XXX 运行完成后必须退出（即在出现按任意键退出时，要按任意键）__
---
## 空指针与void\*类型指针
### 空指针
* 通常说空指针是一个没有指向的指针，即为NULL，这个说法不是很准确，空指针其实也是有指向的指针，但它指向的地址是很小的地址，约定俗成地址为0，在stdio.h的头文件定义中NULL为#define NULL ((void*)0),这是一个宏定义，NULL实际是((void*)0)，这是一个强制类型转换，转换成了void\*类型，本来void\*类型就是用来存放地址的，这里的0自然也就是地址0，在内存分配时，较小的地址是不用来存放数据的，也不允许程序访问，因此这个指针不能操作它指向的这块小的地址，简单来说，空指针其实有指向，但是它指向的地址是特殊的，该地址不允许存放数据和不允许程序访问，所以空指针不能操作该地址里的东西，我们也就理解成“指针指向了空，无法操作了”。
### void\*类型指针
* 这个类型指针指向了实实在在的存放数据的地址，但是类型我们暂时不知道
* 一个例子
```C
char*str=(char*)malloc(sizeof(char)*13);
```
* malloc函数动态分配内存返回的所分配内存的首地址，但不知道该地址存放的内容是什么，此时类型为void\*，后来强制类型转换为char*,使得它可以存放我们想要的内容
----
## memcpy函数
### 函数原型：
```C
void *memcpy(void *str1, const void *str2, size_t n)
```
### 参数
* str1 -- 指向用于存储复制内容的目标数组，类型强制转换为 void* 指针。
* str2 -- 指向要复制的数据源，类型强制转换为 void* 指针。
* n -- 要被复制的字节数。
### 返回值
该函数返回一个指向目标存储区str1的指针
### 实例
```C
// 将字符串复制到数组 dest 中
#include <stdio.h>
#include <string.h>
 
int main ()
{
   //VS2017会提示不能将const char*转换为char*
   const char src[50] = "http://www.runoob.com";
   char dest[50];
 
   memcpy(dest, src, strlen(src)+1);
   printf("dest = %s\n", dest);
   
   return(0);
}

```
### 输出
dest = http://www.runoob.com
---
## memset函数
### 描述
C 库函数 void \*memset(void \*str, int c, size_t n) 复制字符 c（一个无符号字符）到参数 str 所指向的字符串的前 n 个字符。
### 声明
void \*memset(void \*str,int c,size_t n)
### 参数
* str --要填充内存块的起始地址
* c--使用的字符，该值在填充内存块时使用的是它的无符号字符形式
* n--要被设置为该值的字节数
### 返回值
返回一个指向存储区str的指针
### 实例
```C
#include <stdio.h>
#include <string.h>

int main ()
{
   char str[50];

   strcpy(str,"This is string.h library function");
   puts(str);

   memset(str,'$',7);
   puts(str);
   
   return(0);
}
```
### 结果
* This is string.h library function
* $$$$$$$ string.h library function
---------------
