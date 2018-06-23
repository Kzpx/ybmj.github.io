---
title: Bellman-Ford
comments: true
date: 2018-03-09 15:18:06
categories:
- ACM
- 图论
- 最短路
---

# Bellman_Ford
## 思想

实际上就是Floyd_Warshall的单源版本

不断对**边**进行松弛操作，其实就是再求两点间的最小距离，因为每次更新两点之间的最小距离，可能会影响到其它两点间的最小距离。

方程表示就是
$$dp[i][k] = min(dp[i][k],dp[i][t]+dp[t][k])$$

当，$dp[i][t]$或$dp[t][k]$被更新的时候，也许$dp[i][k]$会发生改变。

所以这样一直更新下去，直到没有更新，就结束了。 因为有V个顶点，所以最多更新V-1次。

这个可以这么理解：每次对所有边松弛之后，至少有一个点会被更新。

所以V个点的图，除了起点，就剩下V-1个点了，所以最多更新V-1次。

若更新超过 V-1 次，则说明图中存在负环。

**时间复杂度** : $O(E\times V)$

## 模板
```cpp
struct Edge{
    int u,v,w;
    Edge(int u,int v,int w):u(u),v(v),w(w) {}
};
vector<Edge> edges;

inline void addedge(int u,int v,int w){
    edges.push_back(Edge(u,v,w));
    // edges.push_back(Edge(v,u,w));
}
bool bellman_ford(int s,int n){
    for(int i=0;i<n;i++) d[i] = INF;
    d[s] = 0;
    int cnt = 0;
    while(true){
        if(cnt > n-1) return false;     // exist negative circle
        cnt++;
        bool update = false;
        for(int i=0;i<edges.size();i++){
            int from = edges[i].from;
            int to = edges[i].to;
            if(d[to] > d[from] + edges[i].w){
                d[to] = d[from] + edges[i].w;
                // par[to] = from;         // record path
                update = true;
            }
        }
        if(update == false) break;
    }
    return true;
}
```


## 练习题
