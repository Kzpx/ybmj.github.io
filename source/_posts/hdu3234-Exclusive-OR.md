---
title: hdu3234 Exclusive-OR
comments: true
date: 2018-06-23 22:11:13
categories:
- ACM
- 图论
- 并查集
---

## 分析
带权并查集

添加一个n点，使其值为0，这样任何值异或它都是本身。

然后注意查询的时候，先除去根节点是n的。 然后对于有相同根节点的节点，如果它的数量是奇数那么一定无法确定，偶数则可以确定。

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
const int maxn = 2e4 + 5;
int par[maxn], val[maxn];
int a[30];
bool flag;
int n;

inline int find(int x) {
    if (x == par[x]) return x;
    int fa = par[x];
    par[x] = find(par[x]);
    val[x] ^= val[fa];
    return par[x];
}
inline void merge(int u, int v, int x, int i) {
    int fu = find(u);
    int fv = find(v);
    if (fu == fv) {
        if ((val[u] ^ val[v]) != x) {
            // cout << val[u] << ' ' << val[v] << ' ' << (val[u] ^ val[v]) << endl;
            // cout << u << ' ' << v << ' ' << x << endl;
            flag = true;
            cout << "The first " << i << " facts are conflicting." << endl;
        }
    } else {
        if (fu == n) swap(fu, fv);
        val[fu] = val[u] ^ val[v] ^ x;
        par[fu] = fv;
    }
}
void query(int m) {
    vector<int> fas;
    vector<int> sons[30];
    int ret = 0;
    for (int i = 0; i < m; i++) {
        int fa = find(a[i]);
        if (fa == n)
            ret ^= val[a[i]];
        else {
            int k = 0;
            for (k = 0; k < fas.size(); k++) {
                if (fas[k] == fa) {
                    break;
                }
            }
            if (k == fas.size()) fas.pb(fa);
            sons[k].pb(a[i]);
        }
    }
    for (int i = 0; i < fas.size(); i++) {
        if (sons[i].size() & 1) {
            cout << "I don't know." << endl;
            return;
        }
        for (int k = 0; k < sons[i].size(); k++) {
            ret ^= val[sons[i][k]];
        }
    }
    cout << ret << endl;
}
int main() {
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    std::ios::sync_with_stdio(false);
    int kase = 1;
    int Q;
    while (cin >> n >> Q) {
        flag = false;
        if (!n && !Q) break;
        cout << "Case " << kase++ << ":" << endl;
        for (int i = 0; i <= n; i++) par[i] = i, val[i] = 0;
        int idx = 0;
        string line;
        char op;
        for (int i = 0; i < Q; i++) {
            cin >> line;
            if (line == "I") {
                idx++;
                int k = 0;
                while (cin >> a[k++]) {
                    if (cin.get() == '\n') {
                        break;
                    }
                }
                if(flag) continue;
                if (k == 2)
                    merge(a[0], n, a[1], idx);
                else
                    merge(a[0], a[1], a[2], idx);
            }
            else{
                int k ;
                cin >> k;
                for(int i=0;i<k;i++) cin >> a[i];
                if(flag) continue;
                query(k);
            }
        }
        cout << endl;
    }
}
```
