---
title: cf600E- Lomsat gelral
comments: true
date: 2018-07-22 11:21:44
categories:
- ACM
- 优雅的暴力
- 树上启发式合并(Dsu-on-tree)
---

## 题意
给一棵树，问每个节点的子树中出现颜色次数最多的颜色和。
## 分析
因为要记录出现次数最多的颜色和，所以要有一个数组(times)来记录每个节点的子树中颜色出现最多的次数

这样的话当前的节点可以直接从其重儿子转移过来， 然后在计算其它儿子的同时进行更新。
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
#define my_unique(a) a.resize(distance(a.begin(), unique(a.begin(), a.end())))
#define my_sort_unique(c) (sort(c.begin(), c.end())), my_unique(c)
typedef long long ll;
typedef pair<int, int> pii;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e5 + 5;
ll col[maxn], cnt[maxn], sz[maxn], ans[maxn], times[maxn];
bool vis[maxn], bigc[maxn];
vector<int> G[maxn];

ll szdfs(int u, int fa) {
    sz[u] = vis[u] = 1;
    for (auto &v : G[u])
        if (v != fa && !vis[v]) sz[u] += szdfs(v, u);
    return sz[u];
}
void add(int u, int fa, int x, ll &mx, ll &foo) {
    cnt[col[u]] += x;
    if (x != -1) {
        if (mx < cnt[col[u]]) {
            mx = cnt[col[u]];
            foo = col[u];
        } else if (mx == cnt[col[u]]) {
            foo += col[u];
        }
    }
    for (auto &v : G[u])
        if (v != fa && !bigc[v]) add(v, u, x, mx, foo);
}
void dfs(int u, int fa, bool keep) {
    ll mx = -1, bc = -1;
    for (auto &v : G[u])
        if (v != fa && sz[v] > mx) {
            mx = sz[v];
            bc = v;
        }
    for (auto &v : G[u])
        if (v != fa && v != bc) dfs(v, u, 0);
    mx = 0;
    ll foo = 0;
    if (bc != -1) {
        dfs(bc, u, 1);
        bigc[bc] = 1;
        mx = times[bc];
        foo = ans[bc];
    }
    add(u, fa, 1, mx, foo);
    ans[u] = foo;
    times[u] = mx;

    if (bc != -1) bigc[bc] = 0;
    if (!keep) add(u, fa, -1, mx, foo);
}

int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    int n;
    while (cin >> n) {
        for (int i = 1; i <= n; i++) {
            G[i].clear();
            cin >> col[i];
        }
        for (int i = 0, u, v; i < n - 1; i++) {
            cin >> u >> v;
            G[u].pb(v);
            G[v].pb(u);
        }
        clr(vis, 0);
        szdfs(1, -1);
        dfs(1, -1, 0);
        for (int i = 1; i <= n; i++) {
            cout << ans[i] << ' ';
        }
        cout << endl;
    }
}
```