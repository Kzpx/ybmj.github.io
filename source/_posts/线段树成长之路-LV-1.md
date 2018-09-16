---
title: 线段树成长之路-LV.1
comments: true
date: 2018-09-14 16:12:23
categories:
- ACM
- 数据结构
- 线段树
---

# 染色相关

## 区间内不同种类的颜色

### 题意
给定长度为$N$的序列， $M$次操作，每次可以修改一段区间的颜色，或者查询某段中的颜色的不同种类数。

> 颜色种类小于64

### 分析
用位来记录当前段所有的颜色。

合并的时候直接用或操作即可。

相关例题 POJ2777
### 代码
```cpp
// ybmj
// #include <bits/stdc++.h>
#include <cmath>
#include <cstdio>
#include <iostream>
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
int n, m, Q;
const int maxn = 1e5 + 5;
int seg[maxn << 2], lazy[maxn << 2];

void pushup(int rt) { seg[rt] = seg[lson] | seg[rson]; }
void pushdown(int rt) {
    if (lazy[rt] == -1) return;
    seg[lson] = seg[rson] = lazy[rson] = lazy[lson] = lazy[rt];

    lazy[rt] = -1;
}
void build(int rt, int l, int r) {
    lazy[rt] = -1;
    if (l == r) {
        seg[rt] = 1;
        return;
    }
    int mid = l + r >> 1;
    build(lson, l, mid);
    build(rson, mid + 1, r);
    pushup(rt);
}

void update(int rt, int l, int r, int L, int R, int c) {
    if (l >= L && r <= R) {
        lazy[rt] = seg[rt] = 1 << (c - 1);
        return;
    }
    pushdown(rt);
    int mid = l + r >> 1;
    if (mid >= L) update(lson, l, mid, L, R, c);
    if (mid + 1 <= R) update(rson, mid + 1, r, L, R, c);
    pushup(rt);
}
int query(int rt, int l, int r, int L, int R) {
    if (l >= L && r <= R) {
        return seg[rt];
    }
    pushdown(rt);
    int ret = 0;
    int mid = l + r >> 1;
    if (mid >= L) ret |= query(lson, l, mid, L, R);
    if (mid + 1 <= R) ret |= query(rson, mid + 1, r, L, R);
    return ret;
}

int main() {
    //	/*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    //	*/
    std::ios::sync_with_stdio(false);
    scanf("%d%d%d", &n, &m, &Q);
    char op[2];
    build(1, 1, n);
    for (int i = 0, x, y, z; i < Q; i++) {
        scanf("%s", op);
        if (op[0] == 'C') {
            scanf("%d%d%d", &x, &y, &z);
            if (x > y) swap(x, y);
            update(1, 1, n, x, y, z);
        } else {
            scanf("%d%d", &x, &y);
            if (x > y) swap(x, y);
            int tmp = query(1, 1, n, x, y);
            int cnt = 0;
            for (int i = 0; i < 31; i++) {
                if (tmp & (1 << i)) cnt++;
            }
            printf("%d\n", cnt);
        }
    }
}
```

## 区间内有多少"段"

### 题意
给定长度为$N$的序列， $M$次操作，每次可以修改一段区间的颜色，或者查询区间内有多少段？

> 颜色种类数没有限制
### 分析

线段树每个节点维护三个值，区间内段的数量，最左边的颜色，最右边的颜色。

合并的时候如果交点的颜色相同，则数量要减一

# 矩形面积并

### 分析

将每个矩形的上下两条边取出来。

从下往上扫描。

遇到下边界，则将[l,r)区间覆盖

遇到上边界，则将[l,r)区间撤销

每次查询被覆盖区间的长度。

具体怎么维护呢？
```cpp
struct Node{
    int cnt_cover;  // 区间被覆盖次数
    double length;  // 区间覆盖长度
};
```
所以如果
1. cnt_cover > 0 , 则length = 区间长度
2. cnt_cover == 0 && l == r, 则length = 0
3. 不然的话 length = 左耳子的length + 右儿子的length

**注意**

因为维护的是线段，因此区间都是左闭右开的。 具体实现可以参照代码，不同的地方都有注释标记。

维护线段和维护点是不一样的。

假如你要维护[1,2,3] 你查询的时候应该查到2，但实际上你会查到1，[1,2] 和 [3,3], 所以是不一样的。
### 模板
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
map<double, int> Hash;
map<int, double> rHash;
struct line {
    double l, r, h;
    int val;
    line(double l = 0, double r = 0, double h = 0, int val = 0)
        : l(l), r(r), h(h), val(val) {}
    bool operator<(const line &A) const { return h < A.h; }
};
struct Node {
    int cover;
    double len;
};
const int maxn = 1000;
Node seg[maxn << 2];

void build(int rt, int l, int r) {
    seg[rt].cover = seg[rt].len = 0;
    if (l == r) return;
    int mid = l + r >> 1;
    build(lson, l, mid);
    build(rson, mid + 1, r);
}

void pushup(int rt, int l, int r) {
    if (seg[rt].cover > 0)
        seg[rt].len = rHash[r + 1] - rHash[l];  // [l,r)
    else if (l == r)
        seg[rt].len = 0;
    else
        seg[rt].len = seg[lson].len + seg[rson].len;
}
void update(int rt, int l, int r, int L, int R, int val) {
    if (L <= l && R >= r) {
        seg[rt].cover += val;
        pushup(rt, l, r);
        return;
    }
    int mid = l + r >> 1;
    if (mid >= L) update(lson, l, mid, L, R, val);
    if (mid + 1 <= R) update(rson, mid + 1, r, L, R, val);
    pushup(rt, l, r);
}
int main() {
    //	/*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    //	*/
    std::ios::sync_with_stdio(false);
    int n, kase = 0;
    while (~scanf("%d", &n)) {
        if (!n) break;
        double x1, x2, y1, y2;
        vector<line> a;
        set<double> xval;
        for (int i = 0; i < n; i++) {
            scanf("%lf%lf%lf%lf", &x1, &y1, &x2, &y2);
            a.emplace_back(x1, x2, y1, 1);
            a.emplace_back(x1, x2, y2, -1);
            xval.insert(x1);
            xval.insert(x2);
        }
        // 离散化
        Hash.clear();
        rHash.clear();
        int cnt = 0;
        for (auto &v : xval) {
            Hash[v] = ++cnt;
            rHash[cnt] = v;
        }
        sort(a.begin(), a.end());
        build(1, 1, cnt);
        double ans = 0;
        for (int i = 0; i < a.size() - 1; i++) {
            update(1, 1, cnt, Hash[a[i].l], Hash[a[i].r] - 1,
                   a[i].val);  //[l,r)
            ans += (a[i + 1].h - a[i].h) * seg[1].len;
        }
        printf("Test case #%d\n", ++kase);
        printf("Total explored area: %.2lf\n\n", ans);
    }
}
```