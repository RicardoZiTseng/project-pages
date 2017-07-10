---
layout: post
title: C++程序设计学习笔记（一）
author: Ricardo
tags: C++
---

#### new 与delete操作符

在c语言中，使用malloc()函数动态分配内存：

> ```c++
> int *p = (int *)malloc(sizeof(int)*10);  //分配10个int的内存空间
> free(p);  //释放内存
> ```

在c++中，用new和delete操作符来分配内存：

>```c++
>int *p = new int; //分配内存；
>delete p; //释放内存；
>```

如果想要连续分配内存：

>```c++
>int *p = new int[10];  //分配10个连续的内存空间；
>delete[] p;
>```

与malloc()一样，new在堆区分配内存，必须手动释放，否则就要等到程序结束运行后由操作系统回收。所以new与delete、new[]与delete[]操作符应该成对出现，同时避免与malloc与free混用，c++中推荐使用new与delete，它们可以使用C++的一些新特性，最明显的是可以自动调用构造函数和析构函数。



#### inline内联函数

一个c/c++程序的执行过程可以认为是多个函数之间的相互调用过程，它们形成了一个或简单或复杂的调用链，但是函数调用是有时间和空间开销的，因此c++使用内联函数来消除函数调用时的时空开销。即在编译时将函数调用处用函数体替换，类似c语言中的宏展开。

> ```c++
> #include <iostream>
> using namespace std;
>
> inline void swap(int *a, int *b)
>   {
>     int temp;
>   	temp = *a;
>   	*a = *b;
>   	*b = temp;
>   }
>
> int main(){
>   int m,n;
>   cin>>m>>n;
>   cout<<m<<n<<endl;
>   swap(&m,&n);
>   cout<<m<<n<<endl;
>   return 0;
> }
> ```

- 一定要在函数定义处添加inline关键字，在函数声明处添加inline关键字是无效的；
- 如果内联函数体非常大，又经常使用，那么在主函数体内会有多份相同的拷贝，编译 后的程序也可能会比较大，因此一般只在短小的、频繁调用的函数前加inline关键字；



#### c++函数重载

函数重载的规则：

- 函数名称必须相同
- 参数列表必须不同（个数不同、类型不同、参数排列顺序不同等）。
- 函数的返回类型可以相同也可以不相同。
- 仅仅返回类型不同不足以成为函数的重载。

示例代码：

> ```c++
> #include <iostream>
> using namespace std;
>
> //交换 int 变量的值
> void Swap(int *a, int *b){
>     int temp = *a;
>     *a = *b;
>     *b = temp;
> }
>
> //交换 float 变量的值
> void Swap(float *a, float *b){
>     float temp = *a;
>     *a = *b;
>     *b = temp;
> }
>
> //交换 char 变量的值
> void Swap(char *a, char *b){
>     char temp = *a;
>     *a = *b;
>     *b = temp;
> }
>
> //交换 bool 变量的值
> void Swap(bool *a, bool *b){
>     char temp = *a;
>     *a = *b;
>     *b = temp;
> }
>
> int main(){
>     //交换 int 变量的值
>     int n1 = 100, n2 = 200;
>     Swap(&n1, &n2);
>     cout<<n1<<", "<<n2<<endl;
>    
>     //交换 float 变量的值
>     float f1 = 12.5, f2 = 56.93;
>     Swap(&f1, &f2);
>     cout<<f1<<", "<<f2<<endl;
>    
>     //交换 char 变量的值
>     char c1 = 'A', c2 = 'B';
>     Swap(&c1, &c2);
>     cout<<c1<<", "<<c2<<endl;
>    
>     //交换 bool 变量的值
>     bool b1 = false, b2 = true;
>     Swap(&b1, &b2);
>     cout<<b1<<", "<<b2<<endl;
>
>     return 0;
> }
> ```



默认参数：

所谓默认参数，指的是当函数调用中省略了实参时自动使用的一个值，这个值就是给形参指定的默认值，示例代码：

> ```c++
> #include<iostream>
> using namespace std;
>
> //带默认参数的函数
> void func(int n, float b=1.2, char c='@'){
>     cout<<n<<", "<<b<<", "<<c<<endl;
> }
>
> int main(){
>     func(10, 3.5, '#');     //为所有参数传值
>     func(20, 9.8);	 //为n、b传值，相当于调用func(20, 9.8, '@') 
>     func(30);  //只为n传值，相当于调用func(30, 1.2, '@')
>
>     return 0;
> }
> ```

C++规定，默认参数只能放在形参列表的最后，而且一旦为某个形参指定了默认值，那么它后面的所有形参都必须有默认值。实参和形参的传值是从左到右依次匹配的，默认参数的连续性是保证正确传参的前提。

下面的写法是正确的：

> ```c++
> void func(int a, int b=10, int c=20){ }
> void func(int a, int b, int c=20){ }
> ```

但这样写不可以：

> ```c++
> void func(int a, int b=10, int c=20, int d){ }
> void func(int a, int b=10, int c, int d=20){ }
> ```



