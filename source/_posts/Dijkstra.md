---
title: Dijkstra
comments: true
date: 2018-03-09 15:18:14
categories:
- ACM
- 图论
- 最短路
---

# Dijkstra
## 思想

本质上是个贪心。

从起点开始，依次把连通的路放到优先队列里（权值小的在top),然后每次取top，相当于
连接这条边，再把与边的另一点所连接的边都放到优先队列里。（把已经连接的边看作一个整体）

**注意**: 图中不能存在负权边

**时间复杂度** : $O(E \times logV)$

## 模板
```cpp
typedef pair<int, int> pii;
const int maxn = 100;
int d[maxn];
vector<pii> G[maxn];

inline void addedge(int u, int v, int w) {
    G[u].push_back(make_pair(w, v));
    // G[v].push_back(make_pair(w,u));
}
void dijkstra(int s, int n) {
    for (int i = 0; i < n; i++) d[i] = INF;
    d[s] = 0;
    priority_queue<pii, vector<pii>, greater<pii> > pq;
    pq.push(mp(0, s));
    while (!pq.empty()) {
        pii now = pq.top();
        pq.pop();
        int v = now.second;
        if (d[v] < now.first) continue;  //剪枝！ 重要
        for (int i = 0; i < G[v].size(); i++) {
            pii e = G[v][i];
            int to = e.second;
            if (d[to] > d[v] + e.first) {
                d[to] = d[v] + e.first;
                pq.push(pii(d[to], to));
            }
        }
    }
}

```

