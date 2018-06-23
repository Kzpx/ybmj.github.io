---
title: hdu2473 Junk-Mail Filter 删点
comments: true
date: 2018-06-23 22:05:55
categories:
- ACM
- 图论
- 并查集
---

来源：[题目](https://vjudge.net/problem/HDU-2473)

删点模板题

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
const int mod = 1e9 + 7;
const int maxn = 2e6 + 5;
int par[maxn];
bool vis[maxn];
int idx;
inline int find(int u){
    return u == par[u] ? u : par[u] = find(par[u]);
}
void merge(int u, int v) {
    u = find(u);
    v = find(v);
    if (u != v) {
        par[u] = v;
    }
}
void del(int u) { par[u] = idx++; }
int main() {
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    std::ios::sync_with_stdio(false);

    int n, m;
    int kase = 1;
    while (cin >> n >> m) {
        if (n == 0 && m == 0) break;
        for(int i=0;i<n;i++) par[i] = i+n;
        for(int i=n;i<n+m+n+5;i++) par[i] = i;
        idx = 2*n;
        char op;
        int u, v;
        for (int i = 0; i < m; i++) {
            cin >> op;
            if (op == 'M') {
                cin >> u >> v;
                merge(u, v);
            } else {
                cin >> u;
                del(u);
            }
        }
        clr(vis,0);
        int ans = 0;
        for (int i = 0; i < n; i++) {
            int x = find(i);
            if(!vis[x]){
                vis[x] = true;
                ++ans;
            }
        }
        cout << "Case #" << kase++ << ": ";
        cout << ans << endl;
    }
}
```
