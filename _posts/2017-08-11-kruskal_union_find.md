---
layout: post
author: Ricardo
title: 并查集与Kruskal算法
tags: C++ 图
---

在图的问题中，我们会经常碰到这样一个问题：

​	如何判断两个点是否在一个联通分量中？

​	一个简单暴力的方法就是对图进行dfs或者bfs,但是这样的效率实在太低，而且实现起来比较复杂，所以我们可以使用并查集的方法来处理这个问题。



#### 并查集的概念与实现

​	我们可以把每个联通分量看作一个集合，这个集合中就包含了联通分量中的所有点，这些点两两联通，具体的连通方式不是我们需要考虑的，我们只需要知道在同一个集合中的两个点是相互连通的。

​	假设，一个图包含`1, 2, 3, 4, 5, 6, 7`这七个点，这个图有三个联通分量，`{1, 3}, {2, 5, 6, 7}, {4}`, 其实就是三棵树， 每棵树的根节点就是这棵树所对应集合的代表元。注意：树的具体形态无关紧要，我们只需要知道树里的结点相互连通。

​	假设将结点x的父结点记录在数组p中，那么x的父节点就是p[x]。如果x没有父结点， 那么p[x]等于x。因此查找结点x所在树的根结点的程序为：

```c++
int find_fa(int x){
  return p[x] == x ? x : find_fa(p[x]);
}
```

​	

#### Kruskal算法

​	在介绍Kruskal算法之前，我们先考虑这样一个问题：

​	给定一个无向有权图G = (V, E)，连接图中的所有点，且边集是E的子集的树称为G的生成树，且权值最小的生成树称为最小生成树(Minimal Spanning Tree, MST)， 那么如何构造这样一个MST呢？

​	构造MST的算法很多，其中Kruskal算法最容易实现，也最好编写，而且效率很高。

​	第一步：给图中所有的边从小到大按照顺序排序；

​	第二步：连接图中的连通分量：

​		**情况一：**u和v在同一个联通分量中， 那么加入边(u, v)后形成闭环,因此不能选择;

​		**情况二：** u和v不在一个连通分量中，加入边(u, v) ,这样的解一定是最优的

Kruskal算法的伪代码：

```c++
把所有边排序，记第i小的边为e[i]
初始化MST为空
初始化连通分量，让每个点各自成为一个独立的连通分量
for(int i=0;i<m;i++)
  if(e[i].u 与 e[i].v 不在一个连通分量中){
     把e[i]加入MST中
     合并e[i].u 和 e[i].v所在的联通分量
  }
```

Kruskal算法中的联通分量的查询与合并就可以用到之前讲的并查集来实现:

```c++
//Kruskal算法的具体代码
int cmp(const int i, const int j) { return w[i] < w[j]; }
int find_fa(int x) { return p[x] == x ? x : find_fa(p[x]); }
int Kruskal(){
  int ans = 0;
  for(int i=0; i<n; i++) p[i] = i; //初始化并查集
  for(int i=0; i<m; i++) r[i] = i;	//初始化边序号
  sort(r, r+m, cmp);
  for(int i=0; i<m; i++){
    int e = r[i];
    int x = find_fa(u[e]), y = find_fa(v[e]);		//查找当前两个端点所在的集合编号
    if(x!=y) { ans += w[e]; p[x] = y; }				//若不在一个集合中，就合并
  }
  return ans;
}
```



例题 [Battle Over Cities](https://www.nowcoder.com/pat/1/problem/3991)

​	题目的意思就是给定一个无向图，当去掉一个点时需要连接几条线才能将任意两点连通。

​	这道题其实只需要求出图中的连通块的数目就好了，因为需要连接的线的条数就是连通块的数目减一，下面给出利用并查集实现的代码:

```c++
#include <set>
#include <vector>
#include <cstdio>
#include <cstring>
#include <iostream>
using namespace std;
 
const int maxn = 1000 + 10;
 
struct Edge
{
    int p1, p2;
    Edge(int u, int v):p1(u), p2(v) {}
};
 
set<int> block;			//连通块集合
vector<Edge> edges;		//存储边
int p[maxn];			//存储每个点的父结点
 
int find_sym(int x){			//寻找父结点
    return (p[x] == x)? x : (p[x] = find_sym(p[x]));
}
 
int main(){
    int n, m, k;
    scanf("%d %d %d", &n, &m, &k);
    for(int i=0;i<m;i++){
        int u, v;
        scanf("%d %d", &u, &v);
        edges.push_back(Edge(u, v));
    }
    for(int i=0;i<k;i++){
        int lost_city;
        scanf("%d", &lost_city);
        block.clear();
        memset(p, 0, sizeof(p));
        for(int j=1;j<=n;j++) p[j] = j;			//初始化并查集
        for(int h=0;h<m;h++){	
            Edge e = edges[h];
            if(e.p1==lost_city || e.p2==lost_city) continue;		//当有一个端点是被占领的城市时，则跳过不处理
            else{
                int x = find_sym(e.p1);
                int y = find_sym(e.p2);			//找出当前边的两个结点所在集合编号
                if(x!=y) p[x] = y;				//如果不在一个集合中, 则合并
            }
        }
        for(int j=1;j<=n;j++){
            int symbol = find_sym(j);		//根据并查集查找所有连通块编号，并放入连通块集合中
            if(!block.count(symbol)) block.insert(symbol);
        }
        cout << block.size()-2 << endl;
    }
    return 0;
}
```



这道题还有深度搜索的实现方式:

```c++
#include <cstdio>
#include <cstring>
#include <vector>
using namespace std;
 
const int maxn = 1000 + 10;
int current_point, vis[maxn];
vector<int> G[maxn];
 
void dfs(int p){
    if(p == current_point) return;
    if(vis[p]) return;
    vis[p] = 1;
    for(unsigned int i=0;i<G[p].size();i++){
        dfs(G[p][i]);
    }
}
 
int main(){
    int n, m, k;
    scanf("%d %d %d", &n, &m, &k);
    for(int i=0;i<m;i++){
        int p1, p2;
        scanf("%d %d", &p1, &p2);
        G[p1].push_back(p2);
        G[p2].push_back(p1);
    }
    for(int i=0;i<k;i++){
        int block=0;
        scanf("%d", &current_point);
        memset(vis, 0, sizeof(vis));
        for(int j=1;j<=n;j++){
            if(!vis[j]){
                dfs(j);
                block ++;
            }
        }
        printf("%d\n", block-2);
    }
    if(n && !m && k)
        printf("0\n");
    return 0;
}
```

