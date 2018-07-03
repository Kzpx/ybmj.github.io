---
title: DFS序
comments: true
date: 2018-07-03 09:26:48
categories:
- ACM
- 图论
- DFS序
---

# DFS序

```cpp
// u 的子树区间 [in[u], out[u]]
// 下标从1开始
const int maxn = 100;
vector<int> G[maxn];
int in[maxn], out[maxn];
int dfn;

void init() { dfn = 0; }
void dfs(int u, int fa) {
    in[u] = ++dfn;
    for (auto &v : G[u]) {
        if (v != fa) dfs(v, u);
    }
    out[u] = dfn;
}
```
