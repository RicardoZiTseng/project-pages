---
layout: post
title: C++多线程编程笔记（2）
author: Ricardo
tags: C++ 多线程
---

#### 使用可调用对象

> ```c++
> #include <functional>
> #include <deque>
> #include <iostream>
> #include <string>
> #include <thread>
> #include <mutex>
> #include <fstream>
> #include <condition_variable>
> #include <future>
>
> using namespace std;
>
> class A
> {
> public:
> 	void f(int x, char c){}
> 	int operator()(int N){ return 0; }
> };
>
> void foo(int x){ }
>
> int main(){
> 	A a;
> 	thread t1(a, 6);   //传递a的拷贝给子线程
> 	thread t2(ref(a), 6);  //传递a的引用给子线程
> 	thread t3(move(a), 6);  // a在主线程中不再有效
> 	thread t4(A(), 6);  //传递临时创建的a对象给子线程
> 	thread t5(foo, 6);	//利用全局函数
> 	thread t6([](int x){return x*x; }, 6); //传递lambda函数给子线程
> 	thread t7(&A::f, a, 8, 'w'); //传递a的拷贝的成员函数给子线程
> 	thread t8(&A::f, &a, 8, 'w'); //传递a的地址的成员函数给子线程
>
> 	async(launch::async, a, 6);
> }
> ```
>
> 可以看到，有多种方式可以创建线程。



### packaged_task

> ```c++
> #include <functional>
> #include <queue>
> #include <iostream>
> #include <string>
> #include <thread>
> #include <mutex>
> #include <fstream>
> #include <condition_variable>
> #include <future>
> #include <deque>
>
> using namespace std;
>
> int factorial(int N)
> {
> 	int res = 1;
> 	for (int i = N; i > 1; i--){
> 		res *= i;
> 	}
> 	cout << "result is" << res << endl;
> 	return res;
> }
>
> deque<packaged_task<int()> > task_q;
> mutex mu;
> condition_variable cond;
>
> void thread_1()
> {
> 	packaged_task<int()> t;
> 	{
> 		unique_lock<mutex> locker(mu);
> 		cond.wait(locker, []{return !task_q.empty(); });
> 		t = move(task_q.front());
> 	}
> 	t();
> }
>
> int main(){
> 	thread t1(thread_1);
> 	packaged_task<int()> t(bind(factorial, 6));
> 	future<int> ret = t.get_future(); //获得与packaged_task共享状态相关联的future对象
> 	{
> 		lock_guard<mutex> locker(mu);
> 		task_q.push_back(move(t));
> 	}
> 	cond.notify_one();
> 	int value = ret.get();  //等待任务完成并获取结果
>
> 	t1.join();
>
> 	cout << "value is" << value << endl;
> 	return 0;
> }
> ```
>
> 将factorial函数作为一个参数传给package_task，package_task包括一个可调用对象，并将包装对象的执行结果传递给一个future对象，这个对象通常在另一个线程中获取。
>
> 也就是说，package_task可以实现异步存取。



### 回顾与时间约束

> 线程休眠：
>
> ```c++
> std::this_thread::sleep_for(chrono::milliseconds(3));
> //让线程休眠3毫秒
>
> chrono::steady_clock::time_point tp = chrono::steady_clock::now() + chrono::milliseconds(4); //将休眠完成时间设置到当前时间的4毫秒之后
> this_thread::sleep_until(tp);  //一直休眠到设定时间
> ```
>
> 
>
> 对locker的时间约束：
>
> ```c++
> locker.try_lock_for(chrono::milliseconds(3));
> locker.try_lock_until(tp);
> ```
>
> 
>
> 对condition_variable的时间约束：
>
> ```c++
> cond.wait_for(locker, chrono::milliseconds(4));
> cond.wait_until(locker, tp);
> ```
>
> 
>
> 对future的时间约束：
>
> ```c++
> promise<int> p;
> future<int> f = p.get_future();
> f.wait_for(chrono::miliseconds(3));
> f.wait_until(p);
> ```