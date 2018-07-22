---
title: Dsu-on-tree
comments: true
date: 2018-07-22 10:49:26
categories:
- ACM
- 优雅的暴力
- 树上启发式合并(Dsu-on-tree)
---

## 简介

启发式合并应该比较熟悉，并查集合并的时候为了防止退化，会让高度小的向高度大的合并，这也算一种启发式合并。

树上的启发式合并就是：假设我们现在想知道一棵树上所有节点的子树信息，那么在递归计算的过程中，可以选择保留重儿子的子树上的信息，然后再去重新计算其余儿子的子树信息。这样会使得复杂度由$O(n^2)$降为$O(nlog(n))$

[推荐博客](http://codeforces.com/blog/entry/44351)

## 例题及模板

给一棵树，每个节点都有一个颜色，每次查询问某棵子树上颜色为c的节点数。

```cpp
// 推荐使用
vector<int> *vec[maxn], G[maxn];
int cnt[maxn], sz[maxn], col[maxn];
bool vis[maxn], bigc[maxn];

int szdfs(int u, int fa) {
    sz[u] = vis[u] = 1;
    for (auto &v : G[u])
        if (v != fa && !vis[v]) sz[u] += szdfs(v, u);
    return sz[u];
}

void add(int u, int fa, int x) {
    cnt[col[u]] += x;
    for (auto &v : G[u])
        if (v != fa && !bigc[v]) add(v, u, x);
}

void dfs(int u, int fa, bool keep) {
    int mx = -1, bc = -1;
    for (auto &v : G[u])
        if (v != fa && sz[v] > mx) {
            mx = sz[v];
            bc = v;
        }
    for (auto &v : G[u])
        if (v != fa && v != bc)
            dfs(v, u, 0);  // run a dfs on small childs and clear them from cnt
    if (bc != -1) {
        dfs(bc, u, 1);
        bigc[bc] = 1;
    }  // bigChild marked as big and not cleared from cnt
    add(u, fa, 1);

    // now cnt[c] is the number of vertices in subtree of vertex v that has
    // color c. You can answer the queries easily.
    if (bc != -1) bigc[bc] = 0;
    if (!keep) add(u, fa, -1);
}

```

```cpp
// 多组数据就不要用了...指针可能还要delet？
vector<int> *vec[maxn], G[maxn];
int cnt[maxn], sz[maxn], col[maxn];
bool vis[maxn];

int szdfs(int u, int fa) {
    sz[u] = vis[u] = 1;
    for (auto &v : G[u])
        if (v != fa && !vis[v]) sz[u] += szdfs(v, u);
    return sz[u];
}
void dfs(int u, int fa, bool keep) {
    int mx = -1, bigChild = -1;
    for (auto &v : G[u])
        if (v != fa && sz[v] > mx) mx = sz[v], bigChild = v;
    for (auto &v : G[u])
        if (v != fa && v != bigChild) dfs(v, u, 0);
    if (bigChild != -1)
        dfs(bigChild, u, 1), vec[u] = vec[bigChild];
    else
        vec[u] = new vector<int>();
    vec[u]->push_back(u);
    cnt[col[u]]++;
    for (auto &v : G[u])
        if (v != fa && v != bigChild)
            for (auto x : *vec[v]) {
                cnt[col[x]]++;
                vec[u]->push_back(x);
            }
    // now (*cnt[v])[c] is the number of vertices in subtree of vertex v that
    // has color c. You can answer the queries easily.
    // note that in this step *vec[v] contains all of the subtree of vertex v.
    if (keep == 0)
        for (auto &v : *vec[u]) cnt[col[v]]--;
}
```
