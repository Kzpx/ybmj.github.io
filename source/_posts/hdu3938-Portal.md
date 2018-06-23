---
title: hdu3938 Portal
comments: true
date: 2018-06-23 22:24:31
categories:
- ACM
- 图论
- 并查集
---

来源：[题目](https://vjudge.net/problem/HDU-3938)

## 题意

给你一张无向图，求有多少条路径使得路径上的花费小于L，这里路径上的花费是这样规定的，a、b两点之间的多条路径中的最长的边最小值

## 分析

将所有边按权值排序，从权值最小的边开始取。 如果两点不在同一个集合，则合并（相当于使两个集合的点都可以互达） 答案加 sum[u] * sum[v]

## 代码

```cpp
// ybmj
#include <bits/stdc++.h>
using namespace std;
#define lson (rt << 1)
#define rson (rt << 1 | 1)
#define lson_len (len - (len >> 1))
#define rson_len (len >> 1)
#define clr(a, x) memset(a, x, sizeof(a))
#define mp(x, y) make_pair(x, y)
#define pb(x) push_back(x)
typedef long long ll;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e4 + 5;
int par[maxn];
struct Edge {
    int u, v, w;
    bool operator<(const Edge &A) const { return w < A.w; }
};
struct Query {
    int l, id, ans;
    bool operator<(const Query &A) const { return id < A.id; }
};
Query q[maxn];
vector<Edge> edges;
int sum[maxn];
bool cmpl(const Query A, const Query B) { return A.l < B.l; }
inline int find(int x) { return x == par[x] ? x : par[x] = find(par[x]); }
int main() {
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    std::ios::sync_with_stdio(false);
    int n, m, Q;
    while (cin >> n >> m >> Q) {
        int u, v, w, L;
        edges.clear();
        for (int i = 0; i < m; i++) {
            cin >> u >> v >> w;
            edges.push_back(Edge{u, v, w});
        }
        for (int i = 0; i < Q; i++) {
            cin >> q[i].l;
            q[i].id = i;
            q[i].ans = 0;
        }
        for (int i = 1; i <= n; i++) par[i] = i, sum[i] = 1;
        sort(edges.begin(), edges.end());
        sort(q, q + Q, cmpl);
        int foo = 0;
        for (int i = 0; i < Q; i++) {
            while (foo < m && edges[foo].w <= q[i].l) {
                int u = find(edges[foo].u);
                int v = find(edges[foo].v);
                if (u != v) {
                    par[u] = v;
                    q[i].ans += sum[u] * sum[v];
                    sum[v] += sum[u];
                }
                ++foo;
            }
            if (i) q[i].ans += q[i - 1].ans;
        }
        sort(q, q + Q);
        for (int i = 0; i < Q; i++) cout << q[i].ans << '\n';
    }
}
```