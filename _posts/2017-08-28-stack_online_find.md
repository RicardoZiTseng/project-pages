---
layout: post
author: Ricardo
title: 在线查询的实现
subtitle: 分块思想
tags: 查找
---

​	在有可能随时添加或删除元素的情况下，实时查询序列元素第K大，即把序列元素从小到大排序后从左到右的第K大。例如对于序列`{2, 7, 5, 1, 6}`来说，此时序列第3大是5， 插入元素4之后是4.

​	**在查询的时候元素可能发生改变的查询称为在线查询。**

​	一个最简单暴力的方法就是每次查询前都对新序列排序，但是这样的算法的时间复杂度会很大，因此下面采取分块的思想来解决这个问题(以空间换时间的做法)。

​	分块的意思就是将有序元素分成若干块，每块中有若干有序元素。例如将序列`{1,2,3,5,6,7,9,11,13}`分为3块`{1,2,3} {5,6,7} {9,11,13}`.一般来说为了达到较高的效率，我们会将含有N个元素的序列分成$\lceil\sqrt{N}\rceil$块，每块中元素个数不超过$\lfloor\sqrt{N} + 1\rfloor $。也就是说对于元素个数为9的有序数列来说，分成三块，每块三个元素；对于元素个数是12的序列来说，分成四块，前三块元素个数为3，第四块元素个数为2.

​	下面对一个序列元素个数不超过$10^5$的序列进行讨论：

​	设置一个hash数组table[100001]，其中table[x]表示整数x的当前元素个数，接着利用分块的思想从逻辑上将$0 \backsim10^5$分成$\lceil \sqrt{10^5 + 1}\rceil = 317$块，每块元素个数为$\lfloor \sqrt{10^5 + 1}\rfloor = 316$，逻辑上是这样分块的:

​	0, 1, 2, ..., 315 为第0块；

​	316, 317, ..., 631 为第1块；

​	...

​	同时再定义一个块数组block[317]，其中block[i]代表第i块的元素个数.于是当x加入序列中时，block[x/317]++, table[x]++;每当一个元素x离开序列时， block[x/317]--, table[x]--.

​	如果要查询第K大的元素，应该怎样实现呢？

​	假设某序列为`{1,2,3,4,4,5,7,8}`，查询第5大的元素，先把序列分成3块，第一块和第二块有3个元素，第三块有2个元素，这样的话：

-  block[0] = 3;

​	block[1] = 3;

​	block[2] = 2;

​	table[1] = 1, table[2] = 1, table[4] = 2, table[5] = 1, table[7] = 1, table[8] = 1;

- 然后定义一个变量`sum`记录检查过的序列个数，然后循环检查块元素个数：

​	sum + block[0] = 3 < 5, 因此要检查的元素在之后的块中，sum = 3;

​	sum + block[1] = 6 > 5, 说明要检查的元素在1号块中，因此检查块内的元素个数。

​	(注意：1号块中的元素为`1,2,3`, 2号块中的元素为`4,5,6`,3号块中的元素为`7,8`)

- 首先检查2号块中的元素4：

  sum + table[4] = 5 = 5, 说明要检查的元素就是4

- 如果要检查第4大的元素：

  sum + table[4] = 5 > 4, 说明要检查的元素还是4



这样通过分块的方法可以极大地提高查询的效率。

例题([Stack](https://www.nowcoder.com/pat/5/problem/4110))

````c++
#include <stack>
#include <string>
#include <iostream>
using namespace std;
const int maxn = 100000 + 10;
const int sqrN = 316;
stack<int> s;
int block[sqrN];
int table[maxn];

void Pop(){
	int elem;
	if(s.empty())
		cout << "Invalid" << endl;
	else{
		elem = s.top();
		table[elem] --;
		block[elem/sqrN] --;
		s.pop();
		cout << elem << endl;
	}
}

void Push(){
	int elem;
	cin >> elem;
	table[elem] ++;
	block[elem/sqrN] ++;
	s.push(elem);
}

void PeekMedian(){
	if(s.empty())
		cout << "Invalid" << endl;
	else{
		int sum_ = 0;
		int idx = 0;
		int len = s.size();
		int mid = (len % 2 == 0) ? (len / 2) : ((len + 1) / 2);
		while(sum_ + block[idx] < mid)
			sum_ += block[idx++];
		int num = idx * sqrN;
		while(sum_ + table[num] < mid)
			sum_ += table[num++];
		printf("%d\n", num);
	}
}

int main(){
	int n;
	cin >> n;
	string op;
	while(n--){
		cin >> op;
		if(op[1] == 'o')
			Pop();
		else if(op[1] == 'u')
			Push();
		else if(op[1] == 'e')
			PeekMedian();
	}
	return 0;
}
````