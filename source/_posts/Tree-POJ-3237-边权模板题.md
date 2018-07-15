---
title: Tree POJ - 3237 边权模板题
comments: true
date: 2018-07-10 23:24:31
categories:
- ACM
- 数据结构
- 树链剖分
---

## 题意
三种操作：
1. 改变某条边的权值
2. 将a到b点链上的所有边的权值都取反
3. 查询a到b点链上的所有边的权值最大值

## 分析
将边权下放到点权，注意查询和修改的时候不能动最后的LCA（因为它的权值是属于它与它父亲的边

轻重链剖分后线段树维护即可。

## 代码
```cpp
// ybmj
// #include <bits/stdc++.h>
#include <cstdio>
#include <cstring>
#include <iostream>
#include <vector>
using namespace std;
#define lson (rt << 1)
#define rson (rt << 1 | 1)
#define lson_len (len - (len >> 1))
#define rson_len (len >> 1)
#define pb(x) push_back(x)
#define clr(a, x) memset(a, x, sizeof(a))
#define mp(x, y) make_pair(x, y)
typedef long long ll;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e5 + 5;
int a[maxn];
struct Node {
    int Min, Max;
};
struct Edge {
    int u, v, w;
};
vector<Edge> edges;
struct HLD {
    int n, dfn;
    int sz[maxn], top[maxn], son[maxn], dep[maxn], par[maxn], id[maxn],
        rk[maxn];
    Node seg[maxn << 2];
    int lazy[maxn << 2];
    vector<int> G[maxn];
    void init(int n) {
        this->n = n;
        clr(son, -1);
        dfn = 0;
        for (int i = 0; i <= n; i++) G[i].clear();
    }
    void addedge(int u, int v) {
        G[u].pb(v);
        G[v].pb(u);
    }
    void dfs(int u, int fa, int d) {
        dep[u] = d;
        par[u] = fa;
        sz[u] = 1;
        for (int i = 0; i < G[u].size(); i++) {
            int v = G[u][i];
            if (v == fa) continue;
            dfs(v, u, d + 1);
            sz[u] += sz[v];
            if (son[u] == -1 || sz[v] > sz[son[u]]) son[u] = v;
        }
    }
    void link(int u, int t) {
        top[u] = t;
        id[u] = ++dfn;
        rk[dfn] = u;
        if (son[u] == -1) return;
        link(son[u], t);
        for (int i = 0; i < G[u].size(); i++) {
            int v = G[u][i];
            if (v != son[u] && v != par[u]) link(v, v);
        }
    }
    int pushup(int rt) {
        seg[rt].Min = min(seg[lson].Min, seg[rson].Min);
        seg[rt].Max = max(seg[lson].Max, seg[rson].Max);
    }
    void build(int l, int r, int rt) {
        lazy[rt] = 0;
        if (l == r) {
            seg[rt].Min = seg[rt].Max = a[l];
            return;
        }
        int m = l + r >> 1;
        build(l, m, lson);
        build(m + 1, r, rson);
        pushup(rt);
    }
    void pushdown(int rt) {
        if (!lazy[rt]) return;
        swap(seg[lson].Max, seg[lson].Min);
        seg[lson].Max *= -1;
        seg[lson].Min *= -1;
        swap(seg[rson].Max, seg[rson].Min);
        seg[rson].Max *= -1;
        seg[rson].Min *= -1;
        lazy[lson] ^= 1;
        lazy[rson] ^= 1;
        lazy[rt] = 0;
    }
    void update(int l, int r, int L, int R, int rt, int op, int val) {
        if (L <= l && R >= r) {
            if (!op) {
                seg[rt].Max = seg[rt].Min = val;
            } else {
                swap(seg[rt].Max, seg[rt].Min);
                seg[rt].Max *= -1;
                seg[rt].Min *= -1;
                lazy[rt] ^= 1;
            }
            return;
        }
        pushdown(rt);
        int m = l + r >> 1;
        if (L <= m) update(l, m, L, R, lson, op, val);
        if (R > m) update(m + 1, r, L, R, rson, op, val);
        pushup(rt);
    }
    void update_path(int u, int v, int op) {
        if (!op) {
            update(1, n, id[u], id[u], 1, op, v);
            return;
        }
        if (dep[u] > dep[v]) swap(u, v);

        while (top[u] != top[v]) {
            if (dep[top[u]] < dep[top[v]]) swap(u, v);
            update(1, n, id[top[u]], id[u], 1, op, -1);
            u = par[top[u]];
        }
        if(u == v) return;
        if (dep[u] > dep[v]) swap(u, v);
        update(1, n, id[u] + 1, id[v], 1, op, -1);
    }
    int query(int l, int r, int L, int R, int rt) {
        if (L <= l && R >= r) {
            return seg[rt].Max;
        }
        pushdown(rt);
        int m = l + r >> 1;
        int ret = NINF;
        if (L <= m) ret = max(ret,query(l, m, L, R, lson));
        if (R > m) ret = max(ret,query(m + 1, r, L, R, rson));
        return ret;
    }
    int query_path(int u, int v) {
        int ret = NINF;
        while (top[u] != top[v]) {
            if (dep[top[u]] < dep[top[v]]) swap(u, v);
            ret = max(ret, query(1, n, id[top[u]], id[u], 1));
            u = par[top[u]];
        }
        if(u == v) return ret;
        if (dep[u] > dep[v]) swap(u, v);
        ret = max(ret, query(1, n, id[u] + 1, id[v], 1));
        return ret;
    }
};
HLD hld;

int main() {
    /*
    #ifndef ONLINE_JUDGE
        freopen("1.in", "r", stdin);
        freopen("1.out", "w", stdout);
    #endif
        */
    std::ios::sync_with_stdio(false);
    int T;
    cin >> T;
    while (T--) {
        int n, u, v, w;
        cin >> n;
        hld.init(n);
        edges.clear();
        for (int i = 1; i < n; i++) {
            cin >> u >> v >> w;
            hld.addedge(u, v);
            edges.push_back({u, v, w});
        }
        hld.dfs(1, 1, 1);
        hld.link(1, 1);
        for (int i = 0; i < edges.size(); i++) {
            int &u = edges[i].u;
            int &v = edges[i].v;
            w = edges[i].w;
            if (hld.dep[u] > hld.dep[v]) swap(u, v);
            a[hld.id[v]] = w;
        }
        hld.build(1, n, 1);
        string op;
        while (cin >> op) {
            if (op == "DONE") break;
            if (op == "QUERY") {
                cin >> u >> v;
                cout << hld.query_path(u, v) << endl;
            } else if (op == "CHANGE") {
                cin >> u >> w;
                hld.update_path(edges[u - 1].v, w, 0);
            } else {
                cin >> u >> v;
                hld.update_path(u, v, 1);
            }
        }
    }
}
```
