---
layout: post
author: Ricardo
title: 利用priority_queue实现Dijkstra算法
subtitle: Dijkstra算法的简易实现
tags: C++ 数据结构 图
---

#### Dijkstra算法

这个算法适用于解决边权为正的情况，下面是Dijkstra算法的伪代码：

```
清除所有点的标号
设d[0] = 0, 其他d[i] = INF
循环n次{
  在所有未标号结点中，选出d值最小的结点x
  给结点x标记
  对于从x出发的所有边(x,y)，更新d[y]=min{d[y], d[x]+w(x,y)}
}
```

虽然在图算法中邻接表是一种很流行的表示法，但是概念上vector数组更简单，所以给出基于vector数组的代码：

首先定义一个名为``Edge``的结构体,用来表示边:

````c++
struct Edge{
  int from, to, dist;
  Edge(int u, int v, int d):from(u), to(v), dist(d) {}
}
````

然后将算法中要用到的数据结构封装到一个结构体中:

```c++
struct Dijkstra{
  int n, m;
  vector<Edge> edges;			//存储所有的边
  vector<int> G[maxn];			//存储所有点，并能通过edges查找边的情况
  bool done[maxn];				//记录点的访问情况
  int p[maxn];
  
  void init(int n){
    this->n = n;
    for(int i=0;i<n;i++) G[i].clear();
    edges.clear();
  }
  
  void AddEdge(int from, int to, int dist){
    edges.push_back(Edge(from, to, dist));
    m = edges.size();
    G[from] = m-1;
  }
  
  void dijkstra(int s){
    ...
  }
};
```

在``Dijkstra``算法中，`d[i]`越小，应该越先出队，因此可以使用自定义比较器和优先队列来实现，在STL中，`greater<int>`表示一个“大于”运算符，因此可以用`priority_queue<int, vector<int>, greater<int> >q`来声明一个小整数先出队的优先队列.

但是除了需要求得最小的d值外，还要找到这个最小值对应的结点编号，需要将d值和编号捆绑在一起成为一个整体放到优先队列中，在取出这个d值的同时也能取出对应的结点编号.

在STL中，`pair`便是专门把两个类型捆绑在一起的，`typedef pair<int, int> pii`,则`priority_queue<pii, vector<pii>, greater<pii>`则定义了一个二元组构成的优先队列。

**注意**：`pair`定义了自己的排序规则——先比较第一维，再比较第二维。

但是有时候因为题目的需求,`pair`类型可能不满足我们的要求，或者说是不习惯使用，因此我们可以自己定义一个含有多个元素的结构体：

```c++
struct HeapNode{
  int d, u;				
  bool operator < (const HeapNode& rhs) const{
    return d > rhs.d;   //这个是为了说明d越大，其优先级越小，对应小整数优先；
  }
}；
```

实现主算法：

````c++
void dijkstra(int s){
  priority_queue<HeapNode> Q;
  for(int i=0;i<n;i++) d[i] = INF;
  d[s] = 0;
  memset(done, 0, sizeof(done));
  Q.push((HeapNode){0, s});
  while(!Q.empty()){
    HeapNode x = Q.top();
    Q.pop();
    int u = x.u;
    if(done[u]) continue;
    done[u] = true;
    for(int i=0;i<Q[u].size();i++){
      Edge& e = edges[Q[u][i]];
      if(d[e.to] > d[u] + e.dist){
        d[e.to] = d[u] + e.dist;
        p[e.to] = G[u][i];
        Q.push((HeapNode){d[e.to], e.to});
      }
    }
  }
}
````



#### 例题

[原题链接](https://www.nowcoder.com/pat/1/problem/4017)

**题目描述**

A traveler's map gives the distances between cities along the highways, together with the cost of each highway.  

Now you are supposed to write a program to help a traveler to decide the shortest path between his/her starting city and the destination.

If such a shortest path is not unique, you are supposed to output the one with the minimum cost, which is guaranteed to be unique.



**输入描述**

Each input file contains one test case. Each case starts with a line containing 4 positive integers N, M, S, and D, where N (<=500) is the number of cities (and hence the cities are numbered from 0 to N-1); M is the number of highways; S and D are the starting and the destination cities, respectively.  Then M lines follow, each provides the information of a highway, in the format:

City1 City2 Distance Cost

where the numbers are all integers no more than 500, and are separated by a space.



**输出描述**

For each test case, print in one line the cities along the shortest path from the starting point to the destination, followed by the total distance and the total cost of the path.  The numbers must be separated by a space and there must be no extra space at the end of output.



**例子**

input:

```
4 5 0 3
0 1 1 20
1 3 2 30
0 3 4 10
0 2 2 20
2 3 1 20
```

output:

```
0 2 3 3 40
```

解题思路：这道题是一道经典的`Dijkstra`的题目，直接套用上面的模板就行.



代码：

```c++
#include <iostream>
#include <cstring>
#include <vector>
#include <cstdio>
#include <queue>
using namespace std;

const int maxn = 500 + 10;
const int INF = 0x7fffffff;

typedef pair<int, int> pii;

struct Dijkstra{
  struct Edge{
    int from, to, dist, cost;
    Edge(){}
    Edge(int u, int v, int d, int c):from(u), to(v), dist(d), cost(c){}
    void read(){
      scanf("%d %d %d %d", &from, &to, &dist, &cost);
    }
  };
  int n, m;
  vector<Edge> edges;
  vector<int> G[maxn];
  int d[maxn];
  int done[maxn];
  int pre[maxn];
  int cost[maxn];
  
  void AddEdge(){
    Edge e;
    e.read();
    edges.push_back(e);
    int cnt = edges.size();
    G[e.from].push_back(cnt-1);
  }
  
  void init(int num, int m){
    for(int i=0;i<num;i++) G[i].clear();
    edges.clear();
    for(int i=0;i<m;i++) AddEdge();
  }
  
  void dijkstra(int s){
    priority_queue<pii, vector<pii>, greater<pii> > Q;
    memset(done, 0, sizeof(done));
    memset(d, INF, sizeof(d));
    memset(pre, -1, sizeof(pre));
    d[s] = 0;
    cost[s] = 0;
    Q.push(make_pair(d[s], s));
    while(!Q.empty()){
      pii p = Q.top();
      Q.pop();
      int u = p.second;
      if(done[u]) continue;
      done[u] = 1;
      for(unsigned int i=0;i<G[u].size();i++){
        Edge e = edges[G[u][i]];
        int temp = e.to;
        if(done[temp]) continue;
        if(d[temp] > d[u] + e.dist || (d[temp] == d[u] + e.dist && cost[temp] > cost[u] + e.cost)){
          d[temp] = d[u] + e.dist;
          cost[temp] = cost[u] + e.cost;
          pre[temp] = u;
          Q.push(make_pair(d[temp], temp));
        }
      }
    }
  }
  
  void print_pre(int t){
    if(t == -1) return;
    else{
      print_pre(pre[t]);
      printf("%d ", t);
    }
  }
  
  void print_dist_cost(int t){
    cout << d[t] << " " << cost[t];
  }
};

int main(){
  Dijkstra g;
  int N, M, S, D;
  cin >> N >> M >> S >> D;
  g.init(N, M);
  g.dijkstra(S);
  g.print_pre(D);
  g.print_dist_cost(D);
  return 0;
}
```



#### 邻接表法实现Dijkstra算法

[贴个地址](http://www.61mon.com/index.php/archives/194/#menu_index_1)

邻接表法是比较流行的一种图的表示方法，上面的优先队列实现Dijkstra算法速度较快，不过邻接表的方法也要掌握一下！

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;
const int maxn = 100;
const int INF = 0x7fffffff;

int matrix[maxn][maxn]; //邻接矩阵
int visit[maxn];	//标记是否访问的数组
int dist[maxn];		//源点到顶点i的最短距离
int pre[maxn];		//顶点i的前一个结点
int source;			//源点
int pot_num;		//顶点个数
int path_num;		//弧数

void Dijkstra(int s){
  memset(visited, 0, sizeof(visited));
  visited[s] = 1;
  for(int i=0;i<pot_num;i++){
    dist[i] = matrix[s][i];
    path[i] = s;
  }
  int min_cost;
  int min_cost_index;
  for(int i=1;i<pot_num;i++){
    min_cost = INF;
    for(int j=0;j<pot_num;j++){
      if(visited[j] == 0 && dist[j] < min_cost){
        min_cost = dist[j];
        min_cost_index = j;
      }
    }
    visited[min_cost_index] = 1;
    for(int j = 0;j<pot_num;j++){
      if(visited[j] == 0 && matrix[min_cost_index] + min_cost < dist[j]){
        dist[j] = matrix[min_cost_index] + min_cost;
        path[j] = min_cost_index;
      }
    }
  }
}

int main(){
  cout << "请输入图的顶点个数："<< endl;
  cin >> pot_num;
  cout << "请输入图的弧数："<< endl;
  cin >> path_num;
  memset(matrix, INF, sizeof(matrix));
  cout << "输入弧的信息："<< endl;
  int u, v, w;
  for(int i=0;i<path_num;i++){
    cin >> u >> v >> w;
    matrix[u][v] = matrix[v][u] = w;
  }
  cout << "输入源点(<" << pot_num << "):";
  cin >> source;
  Dijkstra(source);
  for(int i=0;i<pot_num;i++){
    if(i!=source){
       cout << source << "到" << i << "最短距离是：" << dist[i] << "，路径是：" << i;
            int t = path[i];
            while (t != source)
            {
                cout << "--" << t;
                t = path[t];
            }
            cout << "--" << source << endl;
    }
  }
  return 0;
}
```



#### Dijkstra算法的缺陷

![](http://oi0fekpsr.bkt.clouddn.com/%E5%8D%95%E6%BA%90%E6%9C%80%E7%9F%AD%E8%B7%AF%E5%BE%84_4.png?imageView2/2/w/1830/q/75/format/webp)

对于有负权回路的图来说，`Dijkstra`算法就失效了，因为在负权回路中，只要不停地走这条路，就可以不断地减小最短路径权值，其后果就是在这条环路中无限循环，对于这样的图应该采用`Bellman_Ford算法`.

