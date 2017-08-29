---
layout: post
author: Ricardo
title: 背包问题
tag: 动态规划
---

​	背包问题是一类经典的动态规划问题，有两种较为简单的背包问题：**01背包问题与完全背包问题**，其中01背包问题又比较重要。

> 01背包问题：
>
> 有n件物品，每件物品的重量为$w[i]$，价值为$c[i]$.现有一个容量为V的背包，如何选取物品放入背包中，使得背包中物品的总价值最大。其中每种物品都只有1件。

​	如果采取暴力枚举每件物品是否放入背包中，则时间复杂度会达到$O(2^n)$，这是非常糟糕的时间复杂度，使用动态规划的方法可以将时间复杂度降低到$O(nV)$.

​	令$dp[i][v]$表示前i件物品$(1\leq i \leq n, 0 \leq v \leq V)$恰好装入容量为v的背包中所能获得的最大价值 。如何求解$dp[i][v]$?

考虑对第i件物品的选择策略，有两种策略：

- 不放第i件物品， 那么问题转化为前i-1件物品恰好装入容量为v的背包中所能获得的最大价值，即$dp[i-1][v]$
- 放第i件物品，那么问题转化为前i-1件物品恰好装入容量为v-w[i]的背包中所能获得的最大价值，即$dp[i-1][v-w[i]] + c[i]$

由于只有这两种策略，且要求获得最大价值，因此：

​						$dp[i][v] = max\{dp[i-1][v], dp[i-1][v-w[i]] + c[i]\}$	

​									$(1\leq i \leq n, w[i] \leq v \leq V)$

上面这个方程被称为**状态转移方程**。

同时注意到$dp[0][v] = 0 (0 \leq v \leq V)$(也就是说前0件物品放入背包里都只能获得价值0)，因此就可以将dp数组全部递推出来。同时$dp[i][v]$表示的是恰好为v的情况，所以枚举$dp[n][v]$，取它的最大值就可以了。

​	写出代码：

````c++
for(int i=1; i<=n; i++){
  for(int v=w[i]; v<=V; v++){
    dp[i][v] = max(dp[i-1][v], dp[i-1][v-w[i]] + c[i])
  }
}
````

这个算法的空间复杂度为$O(nV)$,实际上还可以优化空间复杂度到$O(V)$.

可以从状态转移方程中发现，$dp[i][]$只与$dp[i-1][]$有关，因此我们可以认为在一次外循环之后，$dp[i-1][]$就再也用不上了，因此状态转移方程可以改为：

​							  $dp[v] = max \{ dp[v], dp[v-w[i]] + c[i]\}$

​									$(1\leq i \leq n, w[i] \leq v \leq V)$

代码要改为：

```c++
for(int i=1; i<=n; i++){
  for(int v=V; v>=w[i]; v--)
    dp[v] = max(dp[v], dp[v-w[i]] + c[i]);
}
```

这种数组被称为**滚动数组**.

注意:使用滚动数组的时候，v要逆序做循环！



例题([Find More Coins](https://www.nowcoder.com/pat/5/problem/4119))

> 这道题的意思就是给出一定数量n的硬币,以及要支付的金额V，检查能否从这堆硬币中凑出所需要支付的金额，如果可以的话输出硬币的有序序列，如果不能就输出"No Solution".

​	在这道题目中，需要支付的金额相当于是背包的容量V，硬币的面额相当于物品的重量和价值，所以在程序中我们定义一个状态数组`dp[V]={0}`, 面额数组`w[n]`,那么状态转移方程可以写为:

​							$dp[v] = max\{dp[v], dp[v-w[i]] + w[i]\}$

​									$(1\leq i \leq n, w[i] \leq v \leq V)$

​	同时还需要开一个bool型的选择数组`choice[n][V]`.当选择第i个硬币总价值为v时, `choice[i][v]=true`,否则的话`choice[i][v]=false`.最后还需要一个bool型的`flag[n]`, `flag[i]==true`表示第i个硬币被选择了,`flag[i]==false`表示第i个硬币没有被选择.具体代码如下：

````c++
#include <cstdio>
#include <algorithm>
using namespace std;
const int maxn = 10001;
const int maxv = 101;
int w[maxn], dp[maxv] = {0};
bool choice[maxn][maxv], flag[maxn];

bool compare(int a, int b){
  return a > b;			//将序列从大到小排列
}

int main(){
  int n, m;
  scanf("%d %d", &n, &m);
  for(int i=1; i<=n; i++)
    scanf("%d", &w[i]);
  sort(w+1, w+1+n, compare);
  for(int i=1; i<=n; i++){
    for(int v=m; v>=w[i]; i++){
      if(dp[v] <= dp[v-w[i]] + w[i]){
        dp[v] = dp[v-w[i]] + w[i];
        choice[i][v] = true;
      }
      else
        choice[i][v] = false;
    }
  }
  if(dp[m]!=m) printf("No Solution\n");
  else{
    int k=n, num=0, v=m;
    while(k>=0){
      if(choice[k][v] == true){
        flag[k] = true;
        num ++;
        v -= w[i];
      }
      else flag[k] = false;
      k--;
    }
    for(int i=n; i>=1; i--){
      if(flag[i]){
        printf("%d", w[i]);
        num--;
        if(num > 0) printf(" ");
      }
    }
    printf("\n");
  }
  return 0;
}
````

