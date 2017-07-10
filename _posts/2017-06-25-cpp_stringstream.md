---
layout: post
title: sstream头文件介绍
author: Ricardo
tags: C++
---

sstream头文件定义了三个类型来支持内存IO，这些类型可以向string写入数据，从string读出数据，就像string是一个IO流一样。

- istringstream从string中读数据
- ostringstream从string中写数据
- stringstream既可以从string读数据也可以向string中写数据


简单起见，先从stringstream讲起：

> <sstream>库中声明的标准类就利用了这一点，自动选择所必需的转换。而且，转换结果保存在stringstream对象的内部缓冲中。你不必担心缓冲区溢出，因为这些对象会根据需要自动分配存储空间

string到int的转换：

> ```c++
> stringstream stream;
> string result ="1000";
> int n = 0;
> stream << result;
> stream >> n;
> ```

- 如果在多次转换中使用同一个stringstream，在每次转换前注意要使用clear()操作。

  ​

**在类型转换中使用模板**

> ```c++
> template<typename T> void to_string_1(string& result, const T& t){
> 	stringstream oss;
> 	oss << t;
> 	oss >> result;
> }
>
> int main(){
> 	string s1, s2, s3;
> 	to_string_1(s1, 10.5);
> 	to_string_1(s2, 100);
> 	to_string_1(s3, true);
> 	cout << s1+ << " " << s2 << " " << s3 << endl;
> 	return 0;
> }
> ```
>
> 可以很方便将多种数值转换为字符串
>
> 当然，也可以定义一个通用的转换模板，用于任意类型的相互转换：
>
> ```c++
> #include <iostream>
> #include <sstream>
> #include <string>
> #include <vector>
>
> using namespace std;
>
> template<typename out_type, typename in_type>
> out_type convert(const in_type& t){
> 	stringstream stream;
> 	stream << t;
> 	out_type result;
> 	stream >> result;
> 	return result;
> }
>
> int main(){
> 	double d;
> 	string salary;
> 	string s = "12.34";
> 	d = convert<double>(s);  //注意这里要标明转换的类型
> 	cout << d << endl;
> 	return 0;
> }
> ```

- stringstream通常是用来做数据转换的，相比c库的转换，它更加安全，自动和直接。

- 再进行多次转换的时候，必须调用stringstream的成员函数clear().

  > ```c++
  > #include <sstream>
  > #include <iostream>
  > int main()
  > {
  >     std::stringstream stream;
  >     int first, second;
  >     stream<< "456"; //插入字符串
  >     stream >> first; //转换成int
  >     std::cout << first << std::endl;
  >     stream.clear(); //在进行多次转换前，必须清除stream
  >     stream << true; //插入bool值
  >     stream >> second; //提取出int
  >     std::cout << second << std::endl;
  > }
  > ```



完整的示例代码：

> ```c++
> #include <iostream>
> #include <string>
> #include <set>
> #include <sstream>
> using namespace std;
>
> set<string> dict;
>
> int main(){
> 	string s, buf;
> 	while (cin >> s){
> 		for (int i = 0; i < s.length(); i++){
> 			if (isalpha(s[i])) s[i] = tolower(s[i]);
> 			else s[i] = ' ';
> 		}
> 		stringstream ss(s);
> 		while (ss >> buf) dict.insert(buf);
> 	}
> 	for (set<string>::iterator it = dict.begin(); it != dict.end(); ++it){
> 		cout << *it << endl;
> 	}
> 	return 0;
> }
> ```
>
> 用于按字典序输出一个文本所有的单词。