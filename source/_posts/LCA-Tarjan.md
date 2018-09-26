---
title: LCA---Tarjan
comments: true
date: 2018-03-28 11:23:45
categories:
- ACM
- 图论
- 最近公共祖先LCA
---
网上看到一篇很详细的blog，带有模拟过程，所以自己就不再写啦（懒癌..

Tarjan算法是离线的，时间复杂度为O(n+q)

转载自 [大神博客](https://www.cnblogs.com/JVxie/p/4854719.html)

假设我们有一组数据 9个节点 8条边 联通情况如下：

1--2，1--3，2--4，2--5，3--6，5--7，5--8，7--9 即下图所示的树

设我们要查找最近公共祖先的点为9--8，4--6，7--5，5--3；

设f[]数组为并查集的父亲节点数组，初始化f[i]=i，vis[]数组为是否访问过的数组，初始为0;　

![](http://ozrmo3j0k.bkt.clouddn.com/LCA-1.jpg)

下面开始模拟过程：

取1为根节点，往下搜索发现有两个儿子2和3；

先搜2，发现2有两个儿子4和5，先搜索4，发现4没有子节点，则寻找与其有关系的点；

发现6与4有关系，但是vis[6]=0，即6还没被搜过，所以不操作；

发现没有和4有询问关系的点了，返回此前一次搜索，更新vis[4]=1；

![](http://ozrmo3j0k.bkt.clouddn.com/LCA-2.jpg)

表示4已经被搜完，更新f[4]=2，继续搜5，发现5有两个儿子7和8;

先搜7，发现7有一个子节点9，搜索9，发现没有子节点，寻找与其有关系的点；

发现8和9有关系，但是vis[8]=0,即8没被搜到过，所以不操作；

发现没有和9有询问关系的点了，返回此前一次搜索，更新vis[9]=1；

表示9已经被搜完，更新f[9]=7，发现7没有没被搜过的子节点了，寻找与其有关系的点；

发现5和7有关系，但是vis[5]=0，所以不操作；

发现没有和7有关系的点了，返回此前一次搜索，更新vis[7]=1；

![](http://ozrmo3j0k.bkt.clouddn.com/LCA-3.jpg)

表示7已经被搜完，更新f[7]=5，继续搜8，发现8没有子节点，则寻找与其有关系的点；

发现9与8有关系，此时vis[9]=1，则他们的最近公共祖先为find(9)=5；

(find(9)的顺序为f[9]=7-->f[7]=5-->f[5]=5 return 5;)

发现没有与8有关系的点了，返回此前一次搜索，更新vis[8]=1；


表示8已经被搜完，更新f[8]=5，发现5没有没搜过的子节点了，寻找与其有关系的点；

![](http://ozrmo3j0k.bkt.clouddn.com/LCA-4.jpg)

发现7和5有关系，此时vis[7]=1，所以他们的最近公共祖先为find(7)=5；

(find(7)的顺序为f[7]=5-->f[5]=5 return 5;)

又发现5和3有关系，但是vis[3]=0，所以不操作，此时5的子节点全部搜完了；

返回此前一次搜索，更新vis[5]=1，表示5已经被搜完，更新f[5]=2；

发现2没有未被搜完的子节点，寻找与其有关系的点；

又发现没有和2有关系的点，则此前一次搜索，更新vis[2]=1；

![](http://ozrmo3j0k.bkt.clouddn.com/LCA-5.jpg)

表示2已经被搜完，更新f[2]=1，继续搜3，发现3有一个子节点6；

搜索6，发现6没有子节点，则寻找与6有关系的点，发现4和6有关系；

此时vis[4]=1，所以它们的最近公共祖先为find(4)=1;

(find(4)的顺序为f[4]=2-->f[2]=2-->f[1]=1 return 1;)

发现没有与6有关系的点了，返回此前一次搜索，更新vis[6]=1，表示6已经被搜完了；

![](http://ozrmo3j0k.bkt.clouddn.com/LCA-6.jpg)

更新f[6]=3，发现3没有没被搜过的子节点了，则寻找与3有关系的点；

发现5和3有关系，此时vis[5]=1，则它们的最近公共祖先为find(5)=1；

(find(5)的顺序为f[5]=2-->f[2]=1-->f[1]=1 return 1;)

发现没有和3有关系的点了，返回此前一次搜索，更新vis[3]=1；

![](http://ozrmo3j0k.bkt.clouddn.com/LCA-7.jpg)

更新f[3]=1，发现1没有被搜过的子节点也没有有关系的点，此时可以退出整个dfs了。

经过这次dfs我们得出了所有的答案，有没有觉得很神奇呢？是否对Tarjan算法有更深层次的理解了呢？

```cpp
// ybmj

vector<pair<int, int> > G[maxn], Q[maxn];
bool vis[maxn];
int par[maxn], dist[maxn], ANS[maxn];

int find(int u) { return u == par[u] ? u : par[u] = find(par[u]); }
void merge(int u, int v) {      	
    u = find(u);
	v = find(v);
	if (u != v) { par[v] = u; }
}


void initn(int n) {
	for (int i = 0; i < n + 1; i++) {
		G[i].clear();
		vis[i] = 0;
		par[i] = i;
	}
}
void initm(int m) {
	for (int i = 0; i < m + 1; i++) { Q[i].clear(); }
}

void add_edge(int u, int v, int w) {
	G[u].pb(mp(v, w));
	G[v].pb(mp(u, w));
}

void add_query(int u, int v, int id) {
	Q[u].pb(mp(v, id));
	Q[v].pb(mp(u, id));
}

void Tarjan(int u, int fa) {
	vis[u] = true;
	for (auto V : G[u]) {
		if (fa != V.first) {
			Tarjan(V.first, u);
			merge(u, V.first);   // 注意合并顺序，一定是v合并到u上
		}
	}
	for (auto V : Q[u]) {
		if (vis[V.first] == true) {
			ANS[V.second] = find(V.first); // 公共祖先为par[v]
		}
	}
}

```
