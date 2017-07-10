---
layout: post
title: C++多线程编程笔记（1）
author: Ricardo
tags: C++ 多线程
---
> 需加入头文件 thread
>
> ```c++
> #include <iostream>
> #include <thread>
> using namespace std;
>
> void function_1(){
> 	cout << "fuck you!" << endl;
> }
>
> int main(){
> 	thread t1(function_1);
> 	t1.detach();
>
> 	//...
> 	if (t1.joinable())
> 	{
> 		t1.join();
> 	}
> 	return 0;
> }
> ```



> 利用创建线程函数 
>
> ```c++
> thread t1(function_1);
> ```



> std::thread::detach
>
> 将本线程从调用线程中分离出来，允许本线程独立执行。(但是当主进程结束的时候，即便是detach（）出去的子线程不管有没有完成都会被强制杀死)



> 线程detach后不能被加入到队列中，如果加入到队列中会发生一些编译bug，此时可用joinable()函数判断是否能够加入
>
> ```c++
> if (t1.joinable())
> 	{
> 		t1.join();
> 	}
> ```



>代码安全的问题
>
>```c++
>#include <iostream>
>#include <thread>
>using namespace std;
>
>void function_1(){
>	cout << "fuck you!" << endl;
>}
>
>int main(){
>	thread t1(function_1);
>  
>	for(int i=0;i<100;i++) \\主进程
>    {
>      cout << "from main:" << i << endl;
>    }
>  
>  	t1.join();
>	return 0;
>}
>```
>
>如果主进程的for循环中抛出异常，那么主进程会立即结束，同时线程也无法顺利执行，因此需要在代码中加入异常捕捉
>
>```c++
>int main(){
>	Fctor fct;
>	thread t1(fct);
>	try{
>		for (int i = 0; i < 100; i++)
>		{
>			cout << "from main:" << i << endl;
>
>		}
>	}
>	catch (...)
>	{
>		t1.join();
>		throw;
>	}
>	t1.join();
>	return 0;
>}
>```



>用类构造线程
>
>```c++
>#include <iostream>
>#include <thread>
>using namespace std;
>
>class Fctor
>{
>public:
>	void operator()(){
>		for (int i = 0; i > -100; i--)
>			cout << "from t1:" << i << endl;
>	}
>};
>
>int main(){
>	// Fctor fct;
>	//thread t1(fct);
>	thread t1((Fctor()));
>	try{
>		for (int i = 0; i < 100; i++)
>		{
>			cout << "from main:" << i << endl;
>
>		}
>	}
>	catch (...)
>	{
>		t1.join();
>		throw;
>	}
>	t1.join();
>	return 0;
>}
>```



>值传递与引用传递
>
>完整代码:
>
>```c++
>#include <iostream>
>#include <thread>
>#include <string>
>using namespace std;
>
>class Fctor
>{
>public:
>	void operator()(string& msg){
>			cout << "from t1:" << msg << endl;
>		msg = "I loe the second one!";
>	}
>};
>
>int main()
>{
>	string s = "I love the first one!";
>	thread t1((Fctor()), ref(s)); //这里是引用传递
>  	/* thread t1((Fctor()), s);*/  //这里用的是值传递
>	t1.join();
>	
>	cout << "from main:" << s << endl;
>}
>```
>
>值传递：
>
>>from t1:I love the first one!
>>from main:I love the first one!
>
>引用传递：
>
>> from t1:I love the first one!
>> from main:I loe the second one!



> thread t2 = t1;
>
> 是错误的指令，线程对象只能被移动不能被赋值
>
> 报错信息：
>
> > error C2280: “std::thread::thread(const std::thread &)”: 尝试引用已删除的函数



> 在线程里采用值传递参数，效率还是比较低下，同时也不想在线程之间共享内存空间，因此去除ref()的使用。
>
> std::move()
>
> thread t1((Fctor()), s) -------> thread t1((Fctor()), move(s));
>
> move将s字符串从主线程移动到子线程，s内容为空
>
> 就像上面所讲的线程只能移动不能赋值，这里可以使用move完成类似赋值的操作：
>
> thread t2 = t1; ---------> thread t2 = move(t1);
>
> * 注意：这里t1已经移动到了t2，t1内为空



> 获取线程的id号
>
> ```c++
> int main()
> {
> 	string s = "I love the first one!";
> 	cout << this_thread::get_id() << endl;  //获取主线程id
> 	thread t1((Fctor()), s);
> 	thread t2 = move(t1);
> 	cout << t2.get_id() << endl;  //获取t2线程id
> 	t2.join();
> }
> ```



> std::thread::hardware_concurrency()
>
> 这个函数告诉我们自己的电脑cpu支持多少个线程并发编程(我的是4...)



### 数据竞争与互斥对象

> 互斥对象的使用
>
> 需要引入互斥信号量的头文件
>
> ```c++
> #include <mutex>
> ```
>
> 使用函数将输出函数的访问限制住：
>
> ```c++
> void shared_print(std::string msg, int id)
> {
>  	mu.lock();  //锁住信号量，禁止其他线程访问cout函数
>   	std::cout << msg << id << std::endl;
>   	mu.unlock(); //当使用完cout后，解锁信号量，允许其他线程访问
> }
> ```
>
> 但是如果处于竞争区的代码发生异常(std::cout << msg << id << std::endl;)，mutex将会被永远锁住，因此不推荐使用lock()与unlock().



> 疑难
>
> ```c++
> std::lock_guard<std::mutex> guard(mu);
> ```



### 死锁

> 有可能因为两个线程互相等待信号量的释放而发生死锁现象
>
> ```c++
> #include <iostream>
> #include <string>
> #include <thread>
> #include <fstream>
> #include <mutex>
>
> using namespace std;
>
> class LogFile
>   {
>     mutex m_mutex1;
>   	mutex m_mutex2;
>   public:
>   	LogFile(){
>       f.open("log.txt");
>   	}
>   	void shared_print1(string id, int value){
>       lock_guard<mutex> locker1(m_mutex1);
>       lock_guard<mutex> locker2(m_mutex2);
>       cout << "From " << id << ":" << value << endl; 
>   	}
>   	void shared_print2(string id, int value){
>       lock_guard<mutex> locker2(m_mutex2);
>       lock_guard<mutex> locker1(m_mutex1);
>       cout << "From " << id << ":" << value << endl;
>   	}
>   };
>
> void function_1(LogFile& log){
>   for(int i=0;i<100;i++){
>     log.shared_print1(string("from function1:"), i);
>   }
> }
>
> int main(){
>   LogFile log;
>   thread t1(function_1, &log);
>   for(int i=0;i>-100;i++){
>     log.shared_print2(string("from main:"), i);
>   }
>   t1.join();
>   return 0;
> }
> ```
>
> > 运行结果：
> >
> > From from t1::0
> >
> > __
>
> 可以发现，无法继续运行下去，这是因为这里的两个线程相互等待各自的信号量，发生了死锁。
>
> 将LogFile类的中的代码改变一下：
>
> ```c++
> class LogFile
> {
> 	mutex m_mutex1;
> 	mutex m_mutex2;
> 	ofstream f;
> public:
> 	LogFile(){
> 		f.open("log.txt");
> 	}
> 	void shared_print1(string id, int value){
> 		lock(m_mutex1, m_mutex2);   //std::lock会控制所有的信号量，有多少信号量就填多少进去
> 		lock_guard<mutex> locker1(m_mutex1, adopt_lock);//std::adopt_lock是告诉locker1只需要控制mutex的控制权，并在完成一个线程时解锁
> 		lock_guard<mutex> locker2(m_mutex2, adopt_lock);
> 		cout << "From" << id << ":" << value << endl;
> 	}
> 	void shared_print2(string id, int value){
> 		lock(m_mutex1, m_mutex2);
> 		lock_guard<mutex> locker2(m_mutex2, adopt_lock);
> 		lock_guard<mutex> locker1(m_mutex1, adopt_lock);	
> 		cout << "From" << id << ":" << value << endl;
> 	}
> };
> ```
>
> 重点：std::lock   std::lock_guard<mutex>



### Unique Lock And Lazy Initialization

> unique lock的弹性使用，unique lock可以弹性指定一个函数线程访问的区域：
>
> ```c++
> class LogFile
>   {
>     mutex m_mutex;
>   	public:
>   		void shared_print(string id, int value){
>           unique_lock<mutex> locker(m_mutex, defer_lock);
>           \\...
>           locker.lock();
>           cout << "From " << id << ":" << value << endl;\\使用unique lock锁住这段区域
>           locker.unlock();
>           \\...
>   		}
>   }
> ```



>重新审视这份代码：
>
>```c++
>class LogFile
>{
>	mutex m_mutex;
>	ofstream f;
>public:
>	LogFile(){
>		f.open("log.txt");
>	}
>	void shared_print(string id, int value){
>		unique_lock<mutex> locker(m_mutex, defer_lock);
>		f << "From" << id << ":" << value << endl;
>	}
>};
>```
>
>LogFile类的构造函数LogFile()告诉我们，无论怎样，这段程序都会打开“log.txt”文件，但是在这里我们只想在产生结果的时候打开文件，因此我们把这段代码这样修改：
>
>```c++
>class LogFile
>{
>	mutex m_mutex;
>  	mutex m_mutex_open;  //限制f打开的信号量
>	ofstream f;
>public:
>	LogFile(){
>		// f.open("log.txt");
>	}
>	void shared_print(string id, int value){
>      	/* 下面这段区域需要分析 */
>		if(!f.is_open())
>          {
>          	unique_lock<mutex> locker(m_mutex_open, defer_lock);
>            f.open("log.txt");  //这里要保证f的打开的线程安全
>          }
>      	/* 上面这段区域需要分析*/
>      	 unique_lock<mutex> locker(m_mutex, defer_lock);
>		f << "From" << id << ":" << value << endl;
>	}
>};
>```
>
> 但是这样修改之后，仍然没有保证线程安全，分析代码中的这一段被标注的区域:
>
>> 假如线程1进入临界区(即被分析的这段区域),如果f没有被打开，那么线程1将信号量m_mutex_open锁住，并打开f，随后线程1离开临界区，并执行日志输出的程序，如果在线程1未打开f时并且信号量被锁住，线程2也来到临界区，发现信号量锁住，那么线程2将会在那里等待，并在线程1离开临界区并且解锁信号量的时候再次锁住信号量，这时线程2也打开f。这样造就的结果就是，当线程1写文件f时，线程2也会插入到线程1后写f，造成混乱。
>>
>> 输出结果：
>>
>> > Fromfrom main::0
>> > FromFromfrom t1:from main:::-11
>> >
>> > FromFromfrom t1:from main:::-22
>> >
>> > FromFromfrom t1:from main:::-33
>> >
>> > FromFromfrom t1:from main:::-44
>> >
>> > FromFromfrom main:from t1:::5-5
>> >
>> > Fromfrom t1::-6
>> > Fromfrom main::6
>> > Fromfrom t1::-7
>> > Fromfrom main::7
>> > FromFromfrom t1:from main:::-88
>> >
>> > FromFromfrom main:from t1:::9-9
>> >
>> > ... ...
>
>解决方案1：
>
>```c++
>class LogFile
>{
>	mutex m_mutex;
>	mutex m_mutex_open;  //限制f打开的信号量
>	ofstream f;
>public:
>	LogFile(){
>		// f.open("log.txt");
>	}
>	void shared_print(string id, int value){
>		unique_lock<mutex> locker1(m_mutex_open, defer_lock);
>		locker1.lock();
>		if (!f.is_open())
>		{	
>			f.open("log.txt");  //这里要保证f的打开的线程安全
>		}
>		unique_lock<mutex> locker(m_mutex, defer_lock);
>		locker.lock();
>		f << "From" << id << ":" << value << endl;
>		cout << "From" << id << ":" << value << endl;
>		locker.unlock();
>		locker1.unlock();
>	}
>};
>```
>
>利用unique_lock的弹性特征，锁住自己想要保护的区域，但是这样比较耗费计算机资源，推荐的做法是下面这种：
>
>解决方案2(貌似存在问题23333)：
>
>```c++
>class LogFile
>{
>	mutex m_mutex;
>  	mutex m_mutex_open;  //限制f打开的信号量
>  	once_flag m_flag;
>	ofstream f;
>public:
>	LogFile(){
>		// f.open("log.txt");
>	}
>	void shared_print(string id, int value){
>      	call_once(m_flag, [&](){f.open("log.txt");});
>      	unique_lock<mutex> locker(m_mutex, defer_lock);
>		f << "From" << id << ":" << value << endl;
>	}
>};
>```

* std::once_flag 
* std::unique_lock



### 条件变量

##### 生产者与消费者模型

> 代码示例：
>
> ```c++
> #include <functional>
> #include <deque>
> #include <iostream>
> #include <string>
> #include <thread>
> #include <mutex>
> #include <fstream>
>
> using namespace std;
>
> deque<int> q;
> mutex mu;
>
> void function_1(){				//function_1 就是生产者
> 	int count = 10;
> 	while (count > 0){
> 		unique_lock<mutex> locker(mu);
> 		q.push_front(count);
> 		locker.unlock();
> 		this_thread::sleep_for(chrono::seconds(1));
> 		count--;
> 	}
> }
>
> void function_2(){				//function_2就是消费者
> 	int data = 0;
> 	while (data != 1){
> 		unique_lock<mutex> locker(mu);
> 		if (!q.empty()){
> 			data = q.back();
> 			q.pop_back();
> 			locker.unlock();
> 			cout << "t2 got a value from t1:" << data << endl;
> 		}
> 		else{
> 			locker.unlock(); 
> 		}
> 	}
> }
>
> int main(){
> 	thread t1(function_1);
> 	thread t2(function_2);
> 	t1.join();
> 	t2.join();
> 	return 0;
> }
> ```
>
> function_1每隔1秒就将一个数字放入队列中，而  function_2 则不断检测队列中是否有数字能够取出来。
>
> 但是不断执行空循环消耗计算机资源，同时效率也低下，因此需要改部分代码：
>
> ```c++
> void function_2(){
> 	int data = 0;
> 	while (data != 1){
> 		unique_lock<mutex> locker(mu);
> 		if (!q.empty()){
> 			data = q.back();
> 			q.pop_back();
> 			locker.unlock();
> 			cout << "t2 got a value from t1:" << data << endl;
> 		}
> 		else{
> 			locker.unlock(); 
> 			this_thread::sleep_for(chrono::milliseconds(10)); //若队列为空，那么就等待十秒再去查看队列是否有数字，这样不会占用计算机资源
> 		}
> 	}
> }
> ```
>
> 但是这里还有个问题就是，我们怎样估计线程睡眠的时间？过长过短都不行，因此我们引入条件变量来解决这个问题。
>
> ```c++
> deque<int> q;
> mutex mu;
> condition_variable cond;    //条件变量的设置
>
> void function_1(){
> 	int count = 10;
> 	while (count > 0){
> 		unique_lock<mutex> locker(mu);
> 		q.push_front(count);
> 		locker.unlock();
> 		cond.notify_one();  //会激活一个等待这个条件的线程
> 		this_thread::sleep_for(chrono::seconds(1));
> 		count--;
> 	}
> }
>
> void function_2(){
> 	int data = 0;
> 	while (data != 1){
> 		unique_lock<mutex> locker(mu);
> 		cond.wait(locker, [](){ return !q.empty();  }); //第二个参数用来防止伪激活的发生
> 		data = q.back();
> 		q.pop_back();
> 		locker.unlock();
> 		cout << "t2 got a value from t1:" << data << endl;
> 	}
> }
> ```
>
> 如果要激活所有等待线程，则使用cond.notify_all()



### Future, Promise And async()

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
> int factorial(int N){
> 	int res = 1;
> 	for (int i = N; i >= 1; i--){
> 		res = res * i;
> 	}
> 	cout << "result is:" << res << endl;
> 	return res;
> }
>
> int main()
> {
> 	int x;
> 	future<int> fu = async(factorial, 4);
> 	x = fu.get();  //future的get函数只能被调用一次，如果调用两次，程序会崩溃；
> 	cout << "x is:" << x << endl;
> 	return 0;
> }
> ```
>
> 但是async不一定创建子线程来实现factorial函数，它的具体实现方式取决于它的第一个参数，可以这样修改：
>
> ```c++
> future<int> fu = async(launch::async, factorial, 4);
> ```
>
> 这样，async会启动一个新线程来启动factorial。
>
> 上面这些告诉我们如何实现线程间的通信，future可以从父线程中获得子线程的变量。
>
> 那么如何从子线程中获得父线程的变量？
>
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
> int factorial(future<int>& f){
> 	int res = 1;
> 	int N = f.get();
> 	for (int i = N; i >= 1; i--){
> 		res = res * i;
> 	}
> 	cout << "result is:" << res << endl;
> 	return res;
> }
>
> int main()
> {
> 	int x;
> 	promise<int> p;
> 	future<int> f = p.get_future();
> 	future<int> fu = async(launch::async, factorial, ref(f));
>
> 	p.set_value(4);   //如果p没有设定值，那么在子程序中的f.get()处会报错，因此一定要注意，promise变量需要设定数值
> 	x = fu.get();
> 	cout << "x is:" << x << endl;
> 	return 0;
> }
> ```



> 如果要实现多个线程，并且欲在子线程中获得父线程中获得变量，是否需要设定多个promise和多个future来实现这样的操作呢？
>
> 答案是否定的。
>
> c++标准库里提供了这样的函数来实现这样的功能：
>
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
> int factorial(shared_future<int> sf){
> 	int res = 1;
> 	int N = sf.get();
> 	for (int i = N; i >= 1; i--){
> 		res = res * i;
> 	}
> 	cout << "result is:" << res << endl;
> 	return res;
> }
>
> int main()
> {
> 	int x;
> 	promise<int> p;
> 	future<int> f = p.get_future();
> 	shared_future<int> sf = f.share();
> 	future<int> fu = async(launch::async, factorial, sf);
> 	future<int> fu2 = async(launch::async, factorial, sf);
> 	future<int> fu3 = async(launch::async, factorial, sf);
>
>
> 	p.set_value(4);
> 	x = fu.get();  //future的get函数只能被调用一次，如果调用两次，程序会崩溃；
> 	cout << "x is:" << x << endl;
> 	return 0;
> }
> ```
>
> 输出结果：
>
> > result is:24result is:24result is:24
> >
> >
> > x is:24



