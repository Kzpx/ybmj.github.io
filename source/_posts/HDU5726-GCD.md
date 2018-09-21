---
title: HDU5726-GCD
comments: true
date: 2018-09-19 22:07:16
categories:
- ACM
- 数据结构
- 线段树
---

## 题意
给一个长度为$N$的序列，每次查询整个序列有多少子段的gcd与$[l,r]$的gcd相同？

$1 \leq N \leq 1e5, 1 \leq a_i \leq 1e9$

## 分析

区间GCD可以由线段树或者ST表来维护。

整个序列的GCD可以预处理出来， 因为GCD不会很多.... 

$dp[i]$ 表示到第i个位置并以第i个位置结尾的gcd集合
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
#define my_unique(a) a.resize(distance(a.begin(), unique(a.begin(), a.end())))
#define my_sort_unique(c) (sort(c.begin(), c.end())), my_unique(c)
const double PI = acos(-1.0);
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e5 + 5;
int a[maxn];
int seg[maxn << 2];

inline void pushup(int rt) { seg[rt] = __gcd(seg[lson], seg[rson]); }
void build(int rt, int l, int r) {
    if (l == r) {
        seg[rt] = a[l];
        return;
    }
    int mid = l + r >> 1;
    build(lson, l, mid);
    build(rson, mid + 1, r);
    pushup(rt);
}
int query(int rt, int l, int r, int L, int R) {
    if (L <= l && R >= r) return seg[rt];
    int ret = 0;
    int mid = l + r >> 1;
    if (L <= mid) ret = __gcd(ret, query(lson, l, mid, L, R));
    if (R >= mid + 1) ret = __gcd(ret, query(rson, mid + 1, r, L, R));
    return ret;
}
map<int, ll> dp[maxn];  // 到i 所组成的gcd的集合和数量

int main() {
    //	/*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    //	*/
    std::ios::sync_with_stdio(false);
    int T, kase = 0;
    scanf("%d", &T);
    while (T--) {
        int n, m;
        scanf("%d", &n);
        for (int i = 1; i <= n; i++) scanf("%d", a + i), dp[i].clear();
        build(1, 1, n);
        //---
        map<int, ll> cnt;
        for (int i = 1; i <= n; i++) {
            cnt[a[i]]++;
            dp[i][a[i]]++;
            for (auto &v : dp[i - 1]) {
                int gcd = __gcd(a[i], v.first);
                cnt[gcd] += v.second;
                dp[i][gcd] += v.second;
            }
        }
        //---
        scanf("%d", &m);
        int l, r;
        printf("Case #%d:\n", ++kase);
        while (m--) {
            scanf("%d%d", &l, &r);
            int gcd = query(1, 1, n, l, r);
            printf("%d %lld\n", gcd, cnt[gcd]);
        }
    }
}
```
