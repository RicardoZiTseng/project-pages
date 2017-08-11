---
layout: post
author: Ricardo
title: Public Bike Management
subtitle: Dijkstra算法的应用
tags: C++ 图
---

[Public Bike Management](https://www.nowcoder.com/pat/5/problem/4324)

![](http://uploadfiles.nowcoder.com/images/20150613/805592_1434175901517_1018.jpg)

​	读完题目我们知道，从PBMC出发携带一定数量的单车，能够满足在抵达目标站点的路径上的所有站点的单车数量能够调整到最优，这条路径同时也要求是最短路，同时也要求到达目标站点后，剩下要带回PBMC的单车数量最少。

​	这么来说，这道题要满足以下几个条件：

​	1.从PBMC出发时要携带足够数量的单车，能够使路径上的所有站点的单车数量调整到最优

​	2.PBMC到目标站点的路径是最短路径

​	3.若有若干条最短路径，则选择带回PBMC的单车数量最短的那一条。

​	在做这道题的时候，我们要注意，从PBMC出发后，每经过一个站点就不能掉头返回上一个站点，比如第一个站点有30辆单车，第二个站点有70辆单车，每个站点需要调整到50辆单车，那么即使第二个站点有足够的单车，也不能将第二个站点的车放到第一个站点。因此，需要从PBMC带30辆车放到站点一，然后再从站点二带走20辆车。

​	上面那张图中，有两条最短路径 PBMC `->`S1`->`S3 与 PBMC `->`S2`->`S3，不过走第二条路径的话只需要携带3辆车，因此会选择路径2。

​	在解决这道题前，我们有两个问题需要解决：

​	1.如何选择最短路径能够保证携带的车辆数量最少？

​	2.选择了最短路径之后要如何计算从PBMC带来的车以及要带回PBMC的车数？

​	

​	**第一个问题：**

​	我们必须到意识这样一个问题，若有若干条最短路径，那么拥有最大数量的车的路径应该是我们需要选择的路径。

​	**第二个问题：**

​	当我们计算出最短路径后，从最短路径的第一个站点开始：

​	(1) 如果当前站点的车的数量大于等于需调整的数量，将多出来的车放到下一个站点中

​	(2) 如果当前站点的车的数量小于需调整的数量，bring变量则增加这一部分缺少的数量

​	假设最后一个站点的下一个站点是PBMC，那么PBMC从最后一个站点获得的车的数量就是需要带回的数量。

```c++
#include <queue>
#include <vector>
#include <cstring>
#include <iostream>
using namespace std;
const int maxn = 500 + 10;
const int INF = 0X3F3F3F3F;

typedef pair<int, int> pii;

struct Dijkstra
{
	struct Edge{
		int to, dis;
		Edge(){}
	};

	vector<Edge> G[maxn];
	vector<int> road;
	int current_num[maxn];			//存储着当前站点的车的数量
	int d[maxn], done[maxn];		
	int pre[maxn], cost[maxn];		//pre存储着当前结点的上一个结点的编号
	int c_perfect, n, m;		    //c_perfect 代表站点的最优单车数量

	Dijkstra(){}

	void AddEdge(){
		Edge e;
		int u, v, c;
		cin >> u >> v >> c;
		e.dis = c;
		e.to = v;
		G[u].push_back(e);
		e.to = u;
		G[v].push_back(e);
	}

	void init(int c, int node_num, int edge_num){
		c_perfect = c / 2;
		n = node_num;
		m = edge_num;
		current_num[0] = 0;
		for(int i=1;i<=n;i++) cin >> current_num[i];
		for(int i=0;i<m;i++) AddEdge();
	}

	void dijkstra(int s){
		priority_queue<pii, vector<pii>, greater<pii> > Q;
		memset(d, INF, sizeof(d));
		memset(done, 0, sizeof(done));
		memset(pre, -1, sizeof(pre));
		memset(cost, 0, sizeof(cost));
		d[s] = 0;
		cost[s] = 0;
		Q.push(make_pair(d[s], s));
		while(!Q.empty()){
			pii q = Q.top();
			Q.pop();
			int u = q.second;
			if(done[u]) continue;
			done[u] = 1;
			for(unsigned int i=0;i<G[u].size();i++){
				Edge e = G[u][i];
				int v = e.to;
				if(done[v]) continue;
				if(d[v] > d[u] + e.dis || (d[v] == d[u] + e.dis && cost[v] < cost[u] + current_num[v])){
					d[v] = d[u] + e.dis;
					cost[v] = cost[u] + current_num[v];
					pre[v] = u;
					Q.push(make_pair(d[v], v));
				}
			}
		} 
	}

	void cal_road(int t){			//求得的最短路结点放入road中
		if(t!= 0){
			cal_road(pre[t]);
			road.push_back(t);
		}
	}

	void cal_pro(int t){
		cal_road(t);
		int len = road.size();
		road.push_back(0);
		int bring = 0;
		for(int i=0;i<len;i++){
			int now_pos = road[i];
			int next_pos = road[i+1];
			int need = current_num[now_pos] - c_perfect;
			if(need >= 0) current_num[next_pos] += need;
			else bring -= need;
		}
		cout << bring << " " << 0;
		for(int i=0;i<len;i++) cout << "->" << road[i];
		cout << " " << current_num[0] << endl;
	}
};

int main(){
	int capacity, n, s, m;
	Dijkstra g;
	cin >> capacity >> n >> s >> m;
	g.init(capacity, n, m);
	g.dijkstra(0);
	g.cal_pro(s);
	return 0;
}
```

这道题还可以用DFS解决。