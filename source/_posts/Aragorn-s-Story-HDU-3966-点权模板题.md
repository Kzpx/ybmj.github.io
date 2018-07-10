---
title: Aragorn's Story HDU-3966 点权模板题
comments: true
date: 2018-07-10 23:19:08
categories:
- ACM
- 数据结构
- 树链剖分
---

## 题意
三种操作： 
1. c1 到 c2 链上都增加k
2. c1 到 c2 链上都减少k
3. 查询c1 到c2 链上的和

## 分析
轻重链剖分后用树状数组维护即可。

## 代码
```cpp
// ybmj
#include <bits/stdc++.h>
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
const int maxn = 5e4+5;
int a[maxn];
struct HLD {
    int n, dfn;
    int sz[maxn], top[maxn], son[maxn], dep[maxn], par[maxn], id[maxn],
        rk[maxn];
    vector<int> G[maxn];
    void init(int n) {
        this->n = n;
        clr(son, -1);
        clr(diff, 0);
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
        for (auto &v : G[u]) {
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
        for (auto &v : G[u]) {
            if (v != son[u] && v != par[u]) link(v, v);
        }
    }
    void update_path(int u, int v, int val) {
        while (top[u] != top[v]) {
            if (dep[top[u]] < dep[top[v]]) swap(u, v);
            update(id[top[u]], id[u], val);
            u = par[top[u]];
        }
        if (dep[u] > dep[v]) swap(u, v);
        update(id[u], id[v], val);
    }

    int diff[maxn];

    int lowb(int x) { return x & (-x); }
    int query(int x) {
        int ret = 0;
        for (int i = x; i > 0; i -= lowb(i)) {
			ret += diff[i];
			continue;
		}
        return ret;
    }
    void update(int l, int r, int val) {
        for (int i = l; i <= n; i += lowb(i)) diff[i] += val;
        for (int i = r + 1; i <= n; i += lowb(i)) diff[i] -= val;
    }
};
HLD hld;

int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    int n, m, q;
    while (cin >> n >> m >> q) {
        hld.init(n + 5);
        for (int i = 1; i <= n; i++) cin >> a[i];
        int u, v, w;
        for (int i = 0; i < m; i++) {
            cin >> u >> v;
            hld.addedge(u, v);
        }
        hld.dfs(1, 1, 1);
        hld.link(1, 1);
        for (int i = 1; i <= n; i++) hld.update(hld.id[i], hld.id[i], a[i]);

        string op;
        for (int i = 0; i < q; i++) {
            cin >> op;
            if (op == "I") {
                cin >> u >> v >> w;
                hld.update_path(u, v, w);
            } else if (op == "D") {
                cin >> u >> v >> w;
                hld.update_path(u, v, -w);
            } else {
                cin >> u;
                cout << hld.query(hld.id[u]) << endl;
            }
        }
    }
}
```
