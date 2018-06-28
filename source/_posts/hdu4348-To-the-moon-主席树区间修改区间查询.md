---
title: hdu4348 To the moon 主席树区间修改区间查询
comments: true
date: 2018-06-28 11:34:04
categories:
- ACM
- 数据结构
- 主席树
---

## 题意

要支持区间修改，区间查询，版本回退

## 分析

因为要维护多个版本，这正是主席树做的事情。

难点在区间修改怎么做

普通的线段树做区间修改都会设置lazy标志，然后在更新和查询的时候会下放。

在主席树中同样也要设置lazy标志，但是不会下放。 因为主席树是动态开点的，下放的时候子节点都还没被被创建...

那么如果lazy标志不下放，在查询的时候就要把祖先们的lazy标志都加上。

特别要注意的是pushup的时候也要带上自身的lazy， 因为sum[i] 记录的是整个区间更新之后的值。

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
typedef pair<ll, ll> pll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e5 + 5;
int root[maxn], a[maxn];
int lch[maxn << 5], rch[maxn << 5];
ll sum[maxn << 5], lazy[maxn << 5];
int idx;

inline void pushup(int rt, int len) {
    sum[rt] = sum[lch[rt]] + sum[rch[rt]] + 1LL * len * lazy[rt];   // !
}
void build(int &k, int l, int r) {
    k = ++idx;
    lazy[k] = 0;
    if (l == r) {
        sum[k] = a[l];
        return;
    }
    int m = l + r >> 1;
    build(lch[k], l, m);
    build(rch[k], m + 1, r);
    pushup(k, r - l + 1);
}

inline void newnode(int old, int k) {
    lch[k] = lch[old];
    rch[k] = rch[old];
    sum[k] = sum[old];
    lazy[k] = lazy[old];
}
void update(int old, int &k, int l, int r, int L, int R, int x) {
    k = ++idx;
    newnode(old, k);
    if (L <= l && R >= r) {
        sum[k] += x * (r - l + 1);      
        lazy[k] += x;
        return;
    }
    int m = l + r >> 1;
    if (m >= L) update(lch[old], lch[k], l, m, L, R, x);
    if (m < R) update(rch[old], rch[k], m + 1, r, L, R, x);
    pushup(k, r - l + 1);
}

ll query(int k, int l, int r, int L, int R, ll x) {
    if (L <= l && R >= r) return sum[k] + 1LL * (r - l + 1) * x;    // !
    int m = l + r >> 1;
    x += lazy[k];                                                   // !
    ll ret = 0;
    if (m >= L) ret += query(lch[k], l, m, L, R, x);
    if (m < R) ret += query(rch[k], m + 1, r, L, R, x);
    return ret;
}

int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    int n, m;
    int kase = 0;
    while (cin >> n >> m) {
        if (kase) cout << '\n';
        kase++;
        for (int i = 1; i <= n; i++) cin >> a[i];
        idx = 0;
        build(root[0], 1, n);
        char op;
        int l, r, t;
        ll x;
        int dfn = 0;
        for (int i = 0; i < m; i++) {
            cin >> op;
            if (op == 'Q') {
                cin >> l >> r;
                cout << query(root[dfn], 1, n, l, r, 0) << endl;
            } else if (op == 'C') {
                cin >> l >> r >> x;
                update(root[dfn], root[dfn + 1], 1, n, l, r, x);
                dfn++;
            } else if (op == 'H') {
                cin >> l >> r >> t;
                cout << query(root[t], 1, n, l, r, 0) << endl;
            } else {
                cin >> dfn;
                idx = root[dfn + 1] - root[0];
            }
        }
    }
}
```