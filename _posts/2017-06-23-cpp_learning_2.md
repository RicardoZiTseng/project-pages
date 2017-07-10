---
layout: post
title: C++程序设计学习笔记（二）
author: Ricardo
tags： C++
---
#### 类的定义与对象的创建

类的声明不占用内存空间，而对象是类这种数据类型的一个变量，占用内存空间。

类的定义：

> ```c++
> class Student{
>   public:
>   //成员变量
>   char *name;
>   int age;
>   float score;
>   
>   void imformation(){
>     cout<<name<<"的年龄是"<<age<<",成绩是"<<score<<endl;
>   }
> };
> ```

类可以理解为一种新的数据类型，但是一种复杂的数据类型，包含多种基本类型，同时具有很多基本类型没有的性质。



#### 创建对象

> ```c++
> Student s;  //创建对象
> int a;  //定义整型变量
> Student students[100]; //可以创建对象数组
> ```



#### 访问类的成员

创建对象之后，使用``.``来访问成员变量和成员函数，与使用结构体变量来访问它的成员类似：

> ```c++
> Student s;
> s.name = "Ricardo";
> s.age = 21;
> s.score = 100;
> s.imformation();
> ```

输出结果为：

> Ricardo的年龄是21，成绩是100

类一般定义在函数外部，也可以定义在函数内部，但是很少这样使用。



#### 使用对象指针

创建出来的对象s在栈上分配内存，因此需要使用``&``来获取地址：

> ```c++
> Student s;
> Student *ps = &s;
> ```

ps是一个指针，指向Student类型的数据。

也可以在堆上创建对象，此时使用``new``关键字：

> ```c++
> Student *ps = new Student;
> ```

在堆上创建的对象只能得到一个指向它的指针，因此必须使用一个指针变量来接收这个指针，否则无法找到这个对象。也就是说，使用``new``在堆上创建的对象是匿名的，没法直接使用，必须用一个指针指向它，再借助指针来访问它的成员变量与成员函数。



**栈内存由程序自动管理，不能使用delete删除在栈上创建的对象；堆内存由程序员管理，对象使用完毕后通过delete删除，开发过程中，new和delete成对出现，防治无用内存的堆积。**



对象指针通过箭头``->``来访问对象的成员变量与成员函数，与结构体指针的用法类似：

> ```c++
> ps->name = "Ricardo";
> ps->age = 21;
> ps->score = 100;
> ps->imformation();
> ```

完整例子：

> ```c++
> #include <iostream>
> using namespace std;
> class Student{
>   public:
>   //成员变量
>   char *name;
>   int age;
>   float score;
>   
>   void imformation(){
>     cout<<name<<"的年龄是"<<age<<",成绩是"<<score<<endl;
>   }
> };
>
> int main()
>   {
>     Student *ps = new Student;
>   	ps->name = "Ricardo";
> 	ps->age = 21;
> 	ps->score = 100;
> 	ps->imformation();
>   	delete ps;
>   	return 0;
>   }
> ```

**注意使用完对象之后，用delete回收内存**



#### 析构函数

- 创建对象系统会自动调用构造函数进行初始化，当销毁对象时会调用析构函数来做清理工作，如释放内存、关闭打开的文件。
- 析构函数是一种特殊的成员函数，没有返回值，也不需要程序员调用，在销毁对象的时候自动执行，析构函数的名字与类名相同，只是需要在类名前面加一个``~``符号。
- 析构函数没有参数，不能被重载，因此一个类只有一个析构函数

示例代码：

> ```c++
> #include <iostream>
> using namespace std;
>
> class VLA{
> public:
> 	VLA(int len);
> 	~VLA();
> public:
> 	void input();
> 	void show();
> private:
> 	int *at(int i);
> private:
> 	const int m_len;
> 	int *m_arr;  //数组指针
> 	int *m_p;  //指向数组第i个元素的指针
> };
>
> VLA::VLA(int len) :m_len(len){   //创建数组
> 	if (len > 0){ m_arr = new int[len]; }
> 	else { m_arr = NULL; }
> }
>
> VLA::~VLA(){					//释放内存
> 	delete[] m_arr;
> }
>
> void VLA::input(){
> 	for (int i = 0; m_p = at(i); i++){
> 		cin >> *at(i);
> 	}
> }
>
> void VLA::show(){
> 	for (int i = 0; m_p = at(i); i++){
> 		if (i == m_len - 1) { cout << *at(i) << endl; }
> 		else { cout << *at(i) << ","; }
> 	}
> }
>
> int * VLA::at(int i){
> 	if (!m_arr || i < 0 || i >= m_len) { return NULL; }
> 	else { return m_arr + i; }
> }
>
> int main(){
> 	int n;
> 	cout << "input array length";
> 	cin >> n;
> 	VLA *ar = new VLA(n);
> 	cout << "Input:" << n << "numbers:";
> 	ar->input();
> 	cout << "Elements:";
> 	ar->show();
> 	delete ar;
> 	return 0;
> }
> ```
>
> **at() 函数只在类的内部使用，所以将它声明为 private 属性；m_len 变量不允许修改，所以用 const 限制。**

析构函数的执行时机：

- 在所有函数之外创建的对象是全局对象，它和全局变量类似，在整个程序结束执行时会调用这些对象的析构函数
- 在函数内部创建的对象是局部对象，它与局部变量类似，位于栈区，函数执行结束时会调用这些对象的析构函数
- new创建的对象位于堆区，通过``delete``删除时才会调用析构函数；如果没有``delete``，析构函数就不会被执行

示例代码：

> ```c++
> #include <iostream>
> #include <string>
> using namespace std;
>
> class Demo{
> public:
> 	Demo(string s);
> 	~Demo();
> private:
> 	string m_s;
> };
>
> Demo::Demo(string s):m_s(s){}
> Demo::~Demo(){ cout << m_s << endl; }
>
> void func(){
> 	Demo obj("1");
> }
>
> Demo obj2("2");
>
> int main(){
> 	Demo obj3("3");
> 	Demo *obj4 = new Demo("4");
> 	func();
> 	cout << "main:" << endl;
> 	return 0;
> }
> ```
>
> 结果：
>
> > 1
> > main:
> > 3
> > 2



#### c++ this指针详解

- this 是C++中的一个关键字，也是一个const指针，指向当前对象，可以访问当前对象的所有成员
- 当前对象就是指当前正在使用的对象，例如对于``stu.show()``,stu就是当前对象，this指向的就是stu
- this只能在类的内部使用，可以访问包括private、public、protected属性的成员

示例代码：

> ```c++
> #include <iostream>
> using namespace std;
>
> class Student{
> public:
> 	void setname(char *name);
> 	void setage(int age);
> 	void setscore(float score);
> 	void show();
> private:
> 	char *name;
> 	int age;
> 	float score;
> };
>
> void Student::setname(char *name){
> 	this->name = name;
> }
>
> void Student::setage(int age){
> 	this->age = age;
> }
>
> void Student::setscore(float score){
> 	this->score = score;
> }
>
> void Student::show(){
> 	cout << this->name << "的年龄是" << this->age << "，成绩是" << this->score << endl;
> }
>
> int main(){
> 	Student *pstu = new Student;
> 	pstu->setname("李华");
> 	pstu->setage(16);
> 	pstu->setscore(96.5);
> 	pstu->show();
> 	return 0;
> }
> ```

**注意**：

- this 是 const 指针，它的值是不能被修改的，一切企图修改该指针的操作，如赋值、递增、递减等都是不允许的。
- this 只能在成员函数内部使用，用在其他地方没有意义，也是非法的。
- 只有当对象被创建后 this 才有意义，因此不能在 static 成员函数中使用



#### this的本质

- this实际上是成员函数的一个形参，在调用成员函数时将对象的地址作为实参传递给this，不过this是隐式的，在编译阶段由编译器将它添加到参数列表
- this作为隐式形参，本质上是成员函数的局部变量，因此只能在成员函数内部使用，只有在通过对象调用成员函数时给this赋值
- 成员函数最终会编译成与对象无关的普通函数，除了成员变量，会丢失所有信息，所以编译时要在成员函数中添加一个额外的参数，把当前对象的首地址传入，以此关联成员函数与成员变量，这个额外的参数就是this，是成员函数与成员变量相关联的桥梁



#### C++ static静态成员变量

- 在c++中使用静态成员变量来实现多个对象共享数据的目标

- 被``static``修饰

- static成员变量属于类，不属于某个具体的对象，即使创建多个对象，也只会为static成员变量分配一份内存

  - 所有对象使用的static成员变量都是这份内存中的数据，一旦某个对象更改了其中的一个数据，也会影响到其他对象

- static成员变量在内存分区中的全局数据区分配内存

- static成员变量不再声明类的时候分配，也不在创建对象的时候分配，而是在类外初始化时分配，如果没有在类外初始化，则static成员变量不能使用 

  > 类外初始化：
  >
  > ```c++
  > int Student::m_total=0;
  > ```

- static 成员变量不占用对象的内存，而是在所有对象之外开辟内存，即使不创建对象也可以访问

> ```c++
> #include <iostream>
> using namespace std;
>
> class Student{
> public:
> 	Student(char *name, int age, float score);
> 	void show();
> private:
> 	static int m_total;
> private:
> 	char *m_name;
> 	int m_age;
> 	float m_score;
> };
>
> int Student::m_total = 0;
>
> Student::Student(char *name, int age, float score):
> m_name(name), m_age(age), m_score(score)
> {
> 	m_total++;
> }
>
> void Student::show(){
> 	cout << m_name << "的年龄是" << m_age << "，成绩是" << m_score << "（当前共有" << m_total << "名学生）" << endl;
> }
>
> int main()
> {
> 	(new Student("xiaoming", 20, 89))->show();
> 	(new Student("huanghuang", 21, 96))->show();
> 	(new Student("pipi", 19, 87))->show();
> 	return 0;
> }
> ```
>
> 运行结果：
>
> > xiaoming的年龄是20，成绩是89（当前共有1名学生）
> > huanghuang的年龄是21，成绩是96（当前共有2名学生）
> > pipi的年龄是19，成绩是87（当前共有3名学生）

注意事项：

- 一个类中可以有一个或多个静态成员变量，所有对象都共享这些静态成员变量，都可以引用它

- 普通成员变量在对象创建的时候分配内存，在对象销毁的时候释放内存

  static成员变量在全局数据区分配内存，在程序结束时释放，因此static成员变量不在对象创建或者销毁时分配内存

- 静态成员变量必须在类外初始化

- 静态成员变量可以通过类名访问或者通过对象访问，因为对于不同的对象来说，访问的都是同一块内存



#### C++中的引用的概念与基本使用

引用类似于 Windows 中的快捷方式，一个可执行程序可以有多个快捷方式，通过这些快捷方式和可执行程序本身都能够运行程序；引用还类似于人的绰号（笔名），使用绰号（笔名）和本名都能表示一个人。

> ```c++
> #include <iostream>
> using namespace std;
> int main(){
>     int a = 99;
>     int &b = a;
>     cout<<a<<", "<<b<<endl;
>     cout<<&a<<", "<<&b<<endl;
>     return 0;
> }
> ```
>
> 运行结果：
>
> > 99, 99
> > 0140FC04, 0140FC04

**引用在定义时需要添加``&``，在使用时不能添加``&``，如果加上``&``表示取地址**

由于引用与原始变量都指向同一地址，所以可以通过引用来修改原始变量中所存储的数据：

> ```c++
> #include <iostream>
> using namespace std;
>
> int main(){
>   int a=9;
>   int &b=a;
>   b=47;
>   cout<<a<<","<<b<<endl;
>   return 0;
> }
> ```
>
> 运行结果：
>
> > 47,47



#### 将引用作为函数参数

如果将函数的形参改成引用的形式，那么就可以将形参与实参绑定在一起，让他们指向同一份数据。如此，就可以在函数内部修改函数外部的数据

> ```c++
> #include <iostream>
> using namespace std;
> void swap1(int a, int b);
> void swap2(int *p1, int *p2);
> void swap3(int &a, int &b);
> int main(){
>     int num1, num2;
>     cout<<"Input two integers: ";
>     cin>>num1>>num2;
>     swap1(num1, num2);
>     cout<<num1<<" "<<num2<<endl;
>    
>     cout<<"Input two integers: ";
>     cin>>num1>>num2;
>     swap2(&num1, &num2);
>     cout<<num1<<" "<<num2<<endl;
>    
>     cout<<"Input two integers: ";
>     cin>>num1>>num2;
>     swap3(num1, num2);
>     cout<<num1<<" "<<num2<<endl;
>     return 0;
> }
> //直接传递参数内容
> void swap1(int a, int b){
>     int temp = a;
>     a = b;
>     b = temp;
> }
> //传递指针
> void swap2(int *p1, int *p2){
>     int temp = *p1;
>     *p1 = *p2;
>     *p2 = temp;
> }
> //按引用传参
> void swap3(int &a, int &b){
>     int temp = a;
>     a = b;
>     b = temp;
> } 
> ```
>
> 运行结果：
>
> > Input two integers: 12 34
> > 12 34
> > Input two integers: 88 99
> > 99 88
> > Input two integers: 100 200
> > 200 100

**在讲引用作为函数返回值时应当注意，不能返回局部数据的引用，当函数调用完之后局部数据会被销毁，有可能下次就不会存在了**

