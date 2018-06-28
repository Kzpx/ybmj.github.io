---
title: Kth number
comments: true
date: 2018-06-28 11:21:24
categories:
- ACM
- 数据结构
- 主席树
---

## 题意

求区间第k大 （不带修改
## 分析

一般的权值线段树只能够求全局第k大 （还不如直接排序

怎么求区间第k大呢？

还是权值线段树的思想

初始版本全是0， 第一个版本加入$a_1$,第二个版本加入$a_2$...

那么我要求$[a_i,a_k]$区间的第k大的话，我只需要在查询的时候用第k个版本减去第(i-1)个版本即可。

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
const int maxn = 1e5 + 5;
int root[maxn];
int lch[maxn << 5], rch[maxn << 5], sum[maxn << 5];
int idx;

vector<int> a, b;
inline int getid(int x) {
    return lower_bound(b.begin(), b.end(), x) - b.begin() + 1;
}

// /*
// inline void pushup(int rt) { sum[rt] += sum[lson] + sum[rson]; }
void build(int &k, int l, int r) {
    k = ++idx;
    sum[k] = 0;
    if (l == r) return;
    int m = l + r >> 1;
    build(lch[k], l, m);
    build(rch[k], m + 1, r);
    // pushup(k);
}
// */

void update(int old, int &k, int l, int r, int p, int x) {
    k = ++idx;
    lch[k] = lch[old];
    rch[k] = rch[old];
    sum[k] = sum[old] + x;
    if (l == r) return;
    int m = l + r >> 1;
    if (p <= m) update(lch[old], lch[k], l, m, p, x);
    else update(rch[old], rch[k], m + 1, r, p, x);
}

int query(int l, int r, int L, int R, int k) {
    if (l == r) return l;
    int m = l + r >> 1;
    int cnt = sum[lch[R]] - sum[lch[L]];
    if (k <= cnt) return query(l, m, lch[L], lch[R], k);
	else return query(m+1,r,rch[L],rch[R],k - cnt);
}
int main() {
    // /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("2.out","w",stdout);
    #endif
    // */
    std::ios::sync_with_stdio(false);
    int T;
    cin >> T;
    while (T--) {
        int n, m;
        cin >> n >> m;
        a.resize(n + 5);
        b.resize(n + 5);
        for (int i = 0; i < n; i++) {
            cin >> a[i];
            b[i] = a[i];
        }
        b.resize(distance(b.begin(), unique(b.begin(), b.end())));
        sort(b.begin(), b.end());
        idx = 0;
        build(root[0], 1, b.size());
        for (int i = 1; i <= n; i++) {
            update(root[i - 1], root[i], 1, b.size(), getid(a[i-1]), 1);
        }
        int l, r, k;
        for (int i = 1; i <= m; i++) {
            cin >> l >> r >> k;
            cout << b[query(1, b.size(),root[l-1], root[r], k)-1] << endl;
        }
    }
}
```
