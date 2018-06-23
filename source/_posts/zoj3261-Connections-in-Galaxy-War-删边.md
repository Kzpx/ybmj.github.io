---
title: zoj3261 Connections in Galaxy War 删边
comments: true
date: 2018-06-23 22:08:27
categories:
- ACM
- 图论
- 并查集
---

删边模板题

## 代码
```cpp
// ybmj
// #include <bits/stdc++.h>
#include<iostream>
#include<vector>
#include<cstring>
#include<algorithm>
#include<set>
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
const int maxn = 5e4 + 5;
struct P {
    string op;
    int u, v;
};
P ops[maxn];
int par[maxn], w[maxn];
pii a[maxn];
set<pii> del;

inline int find(int x) { return x == par[x] ? x : par[x] = find(par[x]); }
inline void merge(int u, int v) {
    u = find(u);
    v = find(v);
    if (u == v) return;
    if (w[u] > w[v])
        par[v] = u;
    else if (w[u] == w[v]) {
        if (u > v)
            par[u] = v;
        else
            par[v] = u;
    } else
        par[u] = v;
}

int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);

    int n, m, Q;
    int kase = 0;
    while (cin >> n) {
        if(kase) cout << '\n';
        kase++;
        for (int i = 0; i < n; i++) {
            cin >> w[i];
            par[i] = i;
        }
        cin >> m;
        for (int i = 0; i < m; i++) {
            cin >> a[i].first >> a[i].second;
            if(a[i].first > a[i].second) swap(a[i].first,a[i].second);
        }
        cin >> Q;
        del.clear();
        for (int i = 0; i < Q; i++) {
            cin >> ops[i].op;
            if (ops[i].op == "destroy") {
                cin >> ops[i].u >> ops[i].v;
                if(ops[i].u > ops[i].v) swap(ops[i].u, ops[i].v);
                del.insert(mp(ops[i].u, ops[i].v));
            } else
                cin >> ops[i].u;
        }
        for (int i = 0; i < m; i++) {
            if (del.find(a[i]) != del.end())
                continue;
            // cout << a[i].first << ' ' << a[i].second << endl;
            merge(a[i].first, a[i].second);
        }
        vector<int> ans;
        int u, v;
        for (int i = Q - 1; i >= 0; i--) {
            if (ops[i].op == "query") {
                u = ops[i].u;
                int fa = find(u);
                if (w[fa] > w[u])
                    ans.pb(fa);
                else
                    ans.pb(-1);
            } else {
                u = ops[i].u;
                v = ops[i].v;
                // cout << i << ' ' << u << ' ' << v << endl;
                merge(u, v);
            }
        }
        for(int i=ans.size()-1;i>=0;i--){
            cout << ans[i] << '\n';
        }
    }
}
```