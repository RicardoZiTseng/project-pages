---
layout: post
title: C++程序设计学习笔记（三）
author: Ricardo
tags: C++
---

#### C++ class和struct的区别

C++保留了c语言中的struct关键字，在c++中，struct既可以包含成员变量，又可以包含成员函数。

在c++中，struct和class基本上是通用的，唯有几个细节不一样：

- 使用class时，类中的成员默认是private属性的；

  而是用struct时，结构体中的成员默认是public属性的

- class继承默认是private属性，而struct继承默认是public属性

- class可以使用模板，而struct不可以

**在编写C++代码时，我强烈建议使用 class 来定义类，而使用 struct 来定义结构体，这样做语义更加明确。**



#### string类详解

1.定义string变量（对象）的几种方法：

> ```c++
> #include <iostream>
> #include <string>
> using namespace std;
>
> int main(){
>   string s1;  //s1="";
>   string s2 = "c plus plus";
>   string s3 = s2; //直接将s2赋值给s3
>   string s4(5, 's'); //s4="sssss";
>   return 0;
> }
> ```

字符串长度用string类中的length()函数得到：

> ```c++
> string s = "I love c++!";
> int len = s.length();
> cout << len << endl;  //11
> ```



2.转换为c风格的字符串

string类提供了一个转换函数c_str()，该函数能够将string字符串转换为c风格的字符串，并返回该字符串的const指针(const char*),代码如下：

> ```c++
> string path = "D:\\demo.txt";
> const char* fp = path.c_str();
> cout << fp <<endl;  //输出：D:\\demo.txt
> cout << *fp << endl; //输出：D
> ```



3.string字符串的输入输出

string 类重载了输入输出运算符，可以像对待普通变量那样对待 string 变量，也就是用`>>`进行输入，用`<<`进行输出。（输入运算符``>>``会忽略空格，遇到空格就认为输入结束）



4.访问字符串中的字符

string字符串的访问也像c里的字符串一样访问其中的每一个字符，string的字符串的起始下标仍是从0开始：

> ```c++
> string s = "123456";
> for(int i=0;i<s.length();i++){
>   cout<<s[i]<<" ";
> }
> ```



5.字符串的拼接

对于string类来说，我们可以直接用``+``或者``+=``运算符来拼接字符串

> ```c++
> #include <iostream>
> #include <string>
> using namespace std;
>
> int main(){
> 	string s1 = "first";
> 	string s2 = "second";
> 	char *s3 = "third";
> 	char s4[] = "fourth";
> 	char ch = '@';
>
> 	string s5 = s1 + s2;
> 	string s6 = s1 + s3;
> 	string s7 = s1 + s4;
> 	string s8 = s1 + ch;
>
> 	cout << s5 << endl << s6 << endl << s7 << endl << s8 << endl;
> 	return 0;
> }
> ```
>
> 输出结果:
>
> first second
> first third
> first fourth
> first @



6.字符串的增删改查

- 插入字符串

  使用insert()函数可以在字符串中指定的位置插入临海一个字符串，其原型为：

  > string& insert (size_t pos, const string& str);

  pos表示要插入的位置，即下标；str表示要插入的字符串，可以是string也可以是c风格的字符串。

  > ```c++
  > string s1 = "1234567";
  > string s2 = "aaa";
  > char *s3 = "third";
  > char s4[] = "fourth";
  > s1.insert(5, s3);
  > s2.insert(2, s4);
  > ```

- 删除字符串

  erase()函数可以删除string中的一个子字符串，其原型为：

  > string& erase(size_t pos=0, size_t len = npos);

  pos表示要删除的字符串的起始下标，len表示要删除的子字符串的长度。

  > ```c++
  > string s1, s2, s3;
  > s1 = s2 = s3 = "1234567890";
  > s2.erase(5);  //s2 = "12345";
  > s3.erase(5,3); //s3 = "1234590";
  > ```

- 提取字符串

  substr()用于从string字符串中提取子字符串，其原型为：

  > string substr (size_t pos = 0, size_t len = npos) const;

  pos为要提取的字符串的起始下标，len为要提取的字符串长度

  > ```c++
  > string s1, s2;
  > s1 = "first second third";
  > s2 = s1.substr(6,6); //s2 = "second";
  > ```

  substr()参数处理与erase()类似：

  - 如果pos越界，就抛出异常
  - 如果len越界，会提取从pos到字符串结尾的所有字符

- 字符串查找

  - find()函数

    find()函数用于在string字符串中查找子字符串第一次出现的位置，它的原型为：

    > size_t find(const string& str, size_t pos = 0) const;
    >
    > size_t find(const char* s, size_t pos = 0) const;

    第一个参数是为待查找的子字符串，第二个参数为开始查找的下标，若不指明，则从第0个字符开始查找

    > ```c++
    > string s1 = "first second third";
    > string s2 = "second";
    > int index = s1.find(s2, 5); //index = 6;
    > ```

    如果没有查找到子字符串，那么会返回一个无穷大值 4294967295。

  - rfind()函数

    rfind()函数同样是在字符串中查找子字符串，但是rfind()最多查找到第二个参数处，如果到了第二个参数所指定的下标处还没找到子字符串，则返回一个无穷大的值，其原型为：

    > size_t rfind(const string& str, size_t pos = 0) const;
    >
    > size_t rfind(const char* s, size_t pos = 0) const;

  - find_first_of()函数

    find_first_of() 函数用于查找子字符串和字符串共同具有的字符在字符串中首次出现的位置。

    > ```c++
    > #include <iostream>
    > #include <string>
    > using namespace std;
    >
    > int main(){
    >     string s1 = "first second second third";
    >     string s2 = "asecond";
    >     int index = s1.find_first_of(s2);
    >     if(index < s1.length())
    >         cout<<"Found at index : "<< index <<endl;
    >     else
    >         cout<<"Not found"<<endl;
    >     return 0;
    > }
    > ```
    >
    > 本例中 s1 和 s2 共同具有的字符是 ’s’，该字符在 s1 中首次出现的下标是3，故查找结果返回3。