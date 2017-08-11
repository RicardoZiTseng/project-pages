---
layout: post
author: Ricardo
title:  多项式加法与乘法的实现
tags: C++
---

[Product Polynomial](https://www.nowcoder.com/pat/1/problem/3995)

简单来说，这道题就是求两个多项式的乘积。

输入例子:

> 2 1 2.4 0 3.2
> 2 2 1.5 1 0.5

即：

$2.4x + 3.2$

$1.5x^2 + 0.5x$

输出例子：

> 3 3 3.6 2 6.0 1 1.6

即：

$3.6x^3 + 6.0x^2 + 1.6x$

在c++中可以用map容器储存多项式，再用pair将指数与系数联系在一起插入线性表中：

````c++
#include <map>
#include <vector>
#include <cstdio>
#include <iostream>
using namespace std;

int main(){
  int n, expt;
  float coef;
  map<int, float> p1, p2, p;
  cin >> n;
  while(n--){
    cin >> expt >> coef;
    p1[expt] = coef;
  }
  cin >> n;
  while(n--){
    cin >> expt >> coef;
    p2[expt] = coef;
  }
  for(auto it1 = p1.begin();it1!=p1.end();it1++){
    for(auto it2 = p2.begin();it2!=p2.end();it2++){
      p[it1->first + it2->first] += it1->second * it2->second;
    }
  }
  vector< pair<int, float> > res;
  for(auto it=p.crbegin();it!=p.crend();it++){
    if(it->second!=0){
      res.push_back(make_pair(it->first, it->second));
    }
  }
  cout << res.size();
  for(unsigned int i=0;i<res.size();i++)
    cout << " " << res[i].first << " " << res[i].second;
  cout << endl;
}
````

