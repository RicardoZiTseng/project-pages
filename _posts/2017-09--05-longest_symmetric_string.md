---
layout: post
author: Ricardo
title: 最长回文子串的求解
tag: 动态规划
---

[Longest Symmetric String](https://www.nowcoder.com/pat/5/problem/4027)

​	最长回文子串如果采用暴力求解的方法，枚举端点的时间复杂度为$O(n^2)$,判断回文的时间复杂度为$O(n)$， 总的时间复杂度为$O(n^3)$,这样的事件复杂度很容易超时, 但是如果采用动态规划的方法可以将时间复杂度降低到$O(n^2)$.

​	令$dp[i][j]$表示S[i]到S[j]的子串是否是回文子串,是则为1,否则为0,那么就有:

- 如果$S[i] == S[j]$, 那么只要$S[i+1]$到$S[j-1]$之间是回文子串的话, 那么$S[i]$到$S[j]$也是回文子串

- 如果$S[i] != S[j]$, 那么$S[i]$到$S[j]$之间肯定不是回文子串

  状态转移方程为：

  ​							$dp[i][j]\left\{\begin{matrix}dp[i+1][j-1], S[i] == S[j]\\ 0, S[i] != S[j] \end{matrix}\right.$

  边界条件:

  ​						    $dp[i][i] = 1, dp[i][i+1] = (S[i] == S[j]) ? 1 : 0$

````c++
#include <cstring>
#include <iostream>
#include <string>
using namespace std;
const int maxn = 1000 + 10;
int dp[maxn][maxn];

int main() {
  string line;
  getline(cin, line);
  int ans = 1;
  memset(dp, 0, sizeof(dp));
  for (int i = 0; i < line.size(); i++) {		//初始化边界条件
    dp[i][i] = 1;
    if (i < line.size() - 1) {
      if (line[i] == line[i + 1]) {
        dp[i][i + 1] = 1;
        ans = 2;
      }
    }
  }
  for (int l = 3; l <= line.size(); l++) {		//由于在处理边界条件的时候我们已经处理了子字符串长度为1或2的情况，所以这个时候从3开始枚举
    for (int i = 0; i + l - 1 < line.size(); i++) {  //枚举左端结点
      int j = i + l - 1;
      if (line[i] == line[j] && dp[i + 1][j - 1] == 1) {
        dp[i][j] = 1;
        ans = l;
      }
    }
  }
  printf("%d\n", ans);
  return 0;
}
````