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

# 矩形面积相关

## 矩形面积并

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

## 矩形面积交

### 分析
维护两个值
- $len1$ 表示覆盖次数大于0的区间长度
- $len2$ 表示覆盖次数大于1的区间长度

那么$pushup$的时候就要分类讨论了。

### 代码
```cpp
// ybmj
// hdu 1255
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
const int maxn = 1e6 + 5;
map<double, int> Hash;
map<int, double> rHash;
struct Lines {
    double l, r, h;
    int val;
    bool operator<(const Lines &A) const { return h < A.h; }
};
struct Node {
    int cnt;      //  覆盖次数
    double len1;  // 覆盖次数大于0的长度
    double len2;  // 覆盖次数大于1的长度
};
Node seg[maxn << 2];

void build(int rt, int l, int r) {
    seg[rt].cnt = seg[rt].len1 = seg[rt].len2 = 0;
    if (l == r) return;
    int mid = l + r >> 1;
    build(lson, l, mid);
    build(rson, mid + 1, r);
}
inline void pushup(int rt, int l, int r) {
    if (seg[rt].cnt > 1)
        seg[rt].len1 = seg[rt].len2 = rHash[r + 1] - rHash[l];
    else if (seg[rt].cnt == 1) {
        seg[rt].len1 = rHash[r + 1] - rHash[l];
        if (l == r)
            seg[rt].len2 = 0;
        else
            seg[rt].len2 = seg[lson].len1 + seg[rson].len1;

    } else {
        if (l == r)
            seg[rt].len1 = seg[rt].len2 = 0;
        else {
            seg[rt].len1 = seg[lson].len1 + seg[rson].len1;
            seg[rt].len2 = seg[lson].len2 + seg[rson].len2;
        }
    }
}
void update(int rt, int l, int r, int L, int R, int val) {
    if (L <= l && R >= r) {
        seg[rt].cnt += val;
        pushup(rt, l, r);
        return;
    }
    int mid = l + r >> 1;
    if (L <= mid) update(lson, l, mid, L, R, val);
    if (R >= mid + 1) update(rson, mid + 1, r, L, R, val);
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
    int T;
    scanf("%d", &T);
    while (T--) {
        int n;
        scanf("%d", &n);
        double x1, x2, y1, y2;
        vector<Lines> line;
        set<double> X;
        for (int i = 1; i <= n; i++) {
            scanf("%lf%lf%lf%lf", &x1, &y1, &x2, &y2);
            line.push_back({x1, x2, y1, 1});
            line.push_back({x1, x2, y2, -1});
            X.insert(x1);
            X.insert(x2);
        }
        sort(line.begin(), line.end());
        int cnt = 0;
        Hash.clear();
        rHash.clear();
        for (auto &v : X) Hash[v] = ++cnt, rHash[cnt] = v;
        build(1, 1, cnt);
        double area = 0;
        for (int i = 0; i < line.size() - 1; i++) {
            update(1, 1, cnt, Hash[line[i].l], Hash[line[i].r] - 1,
                   line[i].val);
            area += seg[1].len2 * (line[i + 1].h - line[i].h);
        }
        printf("%.2lf\n", area);
    }
}
```

# 区间更新的一些骚操作

## 将区间元素都变成其根号

### 题意
1. 区间求和
2. 将区间每个元素都变成其根号
### 分析

因为一个数不断的取根号，很快就变成了1. 因此我们在区间更新的时候，只要这个区间的最大值小于等于1（因为有0），那么就去更新到叶。 
否则就不需要更新。

### 代码
```cpp
// ybmj
// bzoj3211
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

struct Node {
    ll sum;
    int Max;
};
Node seg[maxn << 2];
int a[maxn];

inline void pushup(int rt) {
    seg[rt].Max = max(seg[lson].Max, seg[rson].Max);
    seg[rt].sum = seg[lson].sum + seg[rson].sum;
}
void build(int rt, int l, int r) {
    if (l == r) {
        seg[rt].Max = a[l];
        seg[rt].sum = a[l];
        return;
    }
    int mid = l + r >> 1;
    build(lson, l, mid);
    build(rson, mid + 1, r);
    pushup(rt);
}
void update(int rt, int l, int r, int L, int R) {
    if (seg[rt].Max <= 1) return;
    if (l == r) {
        seg[rt].Max = sqrt(seg[rt].Max);
        seg[rt].sum = sqrt(seg[rt].sum);
        return;
    }
    int mid = l + r >> 1;
    if (mid >= L) update(lson, l, mid, L, R);
    if (mid + 1 <= R) update(rson, mid + 1, r, L, R);
    pushup(rt);
}
ll query(int rt, int l, int r, int L, int R) {
    if (L <= l && R >= r) {
        return seg[rt].sum;
    }
    ll ret = 0;
    int mid = l + r >> 1;
    if (mid >= L) ret += query(lson, l, mid, L, R);
    if (mid + 1 <= R) ret += query(rson, mid + 1, r, L, R);
    return ret;
}
int main() {
    //  /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    //  */
    std::ios::sync_with_stdio(false);
    int n, m;
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", a + i);
    scanf("%d", &m);
    build(1, 1, n);
    while (m--) {
        int x, l, r;
        scanf("%d%d%d", &x, &l, &r);
        if (x == 1) {
            printf("%lld\n", query(1, 1, n, l, r));
        } else {
            update(1, 1, n, l, r);
        }
    }
}
```

## 区间gcd，区间加

### 题意
1. 求区间的gcd
2. 区间加val
### 分析
Gcd性质

```
gcd(a,b) = gcd(a,b-a);
gcd(a,b,c,d) = gcd(a,b-a,c-b,d-c)
```

维护一个a数组的差分序列，称作b

那么区间[l,r]加v， 实际上就等于$b[l] + v, b[r+1] - v$.

查询[l,r]的gcd，等于 $Gcd(a[l], Gcd(b[l+1] .. b[r]))$

所以我们要维护原来的a数组，以及差分后的b数组
### 代码
```cpp
// ybmj
// CH 4302
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
const int maxn = 5e5 + 5;
ll seg[maxn << 2];
ll a[maxn], b[maxn];

inline void pushup(int rt) { seg[rt] = gcd(seg[lson], seg[rson]); }
void build(int rt, int l, int r) {
    if (l == r) {
        seg[rt] = b[l];
        return;
    }
    int mid = l + r >> 1;
    build(lson, l, mid);
    build(rson, mid + 1, r);
    pushup(rt);
}
void update_gcd(int rt, int l, int r, int pos, ll x) {
    if (l == r) {
        seg[rt] += x;
        return;
    }
    int mid = l + r >> 1;
    if (mid >= pos)
        update_gcd(lson, l, mid, pos, x);
    else
        update_gcd(rson, mid + 1, r, pos, x);
    pushup(rt);
}
ll query_gcd(int rt, int l, int r, int L, int R) {
    if (L <= l && R >= r) return seg[rt];
    ll ret = 0;
    int mid = l + r >> 1;
    if (mid >= L) ret = __gcd(ret, query_gcd(lson, l, mid, L, R));
    if (mid + 1 <= R) ret = __gcd(ret, query_gcd(rson, mid + 1, r, L, R));
    return ret;
}

ll bit[maxn];
inline int lowb(int x) { return x & (-x); }
ll query(int x) {
    ll ret = 0;
    for (int i = x; i > 0; i -= lowb(i)) ret += bit[i];
    return ret;
}
void update(int l, int r, ll val, int n) {
    for (int i = l; i <= n; i += lowb(i)) bit[i] += val;
    for (int i = r + 1; i <= n; i += lowb(i)) bit[i] -= val;
}

int main() {
    //	/*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    //	*/
    std::ios::sync_with_stdio(false);
    int n, m;
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%lld", a + i);
    for (int i = 1; i <= n; i++) b[i] = a[i] - a[i - 1];
    build(1, 1, n);
    char op[2];
    int l, r;
    ll val;
    while (m--) {
        scanf("%s", op);
        if (op[0] == 'Q') {
            scanf("%d%d", &l, &r);
            if (l == r)
                printf("%lld\n", a[l] + query(l));
            else
                printf("%lld\n", __gcd(a[l] + query(l),
                                       abs(query_gcd(1, 1, n, l + 1, r))));
        } else {
            scanf("%d%d%lld", &l, &r, &val);
            update_gcd(1, 1, n, l, val);
            if (r < n) update_gcd(1, 1, n, r + 1, -val);
            update(l, r, val, n);
        }
    }
}
```
# 维护循环节

### 题意
You have $N$ integers $A_1,A_2,…,A_N$. You are asked to write a program to receive and execute two kinds of instructions:

- $C$ $a$ $b$ means performing $A_i=(A_i^2mod2018)$ for all Ai such that $a≤i≤b$.
- $Q$ $a$ $b$ means query the sum of $A_a,A_{a+1},…,A_b$. Note that the sum is not taken modulo 2018.

$1 \leq n, Q \leq 50000$
### 分析
每个数模2018的最大周期是6. 所以更新6次之后一定进入循环节。

所以对于每个结点，我们要维护一个长度为6的循环节。

如果更新不足6次，则直接暴力更新到叶子结点。 更新到第6次，说明进入循环节，此时维护循环节即可。

需要注意的是在$pushup$的时候要更新 “次数”。

详见代码

### 代码
```cpp
// ybmj
// 2018 ACM/ICPC 上海大都会赛H题
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
const int maxn = 5e4 + 5;
const int mod = 2018;
int a[maxn];
int sum[maxn << 2][6], p[maxn << 2], lazy[maxn << 2], cnt[maxn << 2];

inline void pushup(int rt) {
    cnt[rt] = min(cnt[lson], cnt[rson]);  // 记得更新次数
    if (cnt[rt] < 5) {
        sum[rt][0] = sum[lson][p[lson]] +
                     sum[rson][p[rson]];  //  如果cnt[i] < 5, 那么p[i] = 0
        return;
    }
    p[rt] = 0;
    for (int i = 0; i < 6; i++)  // 更新整个循环节
        sum[rt][i] =
            sum[lson][(p[lson] + i) % 6] + sum[rson][(p[rson] + i) % 6];
}
inline void pushdown(int rt) {
    if (!lazy[rt]) return;
    lazy[lson] += lazy[rt];
    lazy[rson] += lazy[rt];
    (p[lson] += lazy[rt]) %= 6;
    (p[rson] += lazy[rt]) %= 6;
    lazy[rt] = 0;
}
void build(int rt, int l, int r) {
    p[rt] = lazy[rt] = cnt[rt] = 0;
    if (l == r) {
        sum[rt][0] = a[l];
        return;
    }
    int mid = l + r >> 1;
    build(lson, l, mid);
    build(rson, mid + 1, r);
    pushup(rt);
}
void update(int rt, int l, int r, int L, int R) {
    if (l == r) {  //  进循环节前暴力更新到叶子
        cnt[rt]++;
        if (cnt[rt] < 5) {
            sum[rt][0] = sum[rt][0] * sum[rt][0] % mod;
        } else if (cnt[rt] == 5) {
            sum[rt][0] = sum[rt][0] * sum[rt][0] % mod;
            for (int i = 1; i < 6; i++)
                sum[rt][i] = sum[rt][i - 1] * sum[rt][i - 1] % mod;
        } else
            (++p[rt]) %= 6;
        return;
    }
    if (L <= l && R >= r && cnt[rt] >= 5) {  // 进入循环节后才可以直接返回
        lazy[rt]++;
        (++p[rt]) %= 6;
        return;
    }
    pushdown(rt);
    int mid = l + r >> 1;
    if (L <= mid) update(lson, l, mid, L, R);
    if (R >= mid + 1) update(rson, mid + 1, r, L, R);
    pushup(rt);
}
int query(int rt, int l, int r, int L, int R) {
    if (L <= l && R >= r) return sum[rt][p[rt]];
    pushdown(rt);
    int ret = 0;
    int mid = l + r >> 1;
    if (L <= mid) ret += query(lson, l, mid, L, R);
    if (R >= mid + 1) ret += query(rson, mid + 1, r, L, R);
    return ret;
}
int main() {
    //  /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    //  */
    std::ios::sync_with_stdio(false);
    int T, kase = 1;
    scanf("%d", &T);
    while (T--) {
        int n;
        scanf("%d", &n);
        for (int i = 1; i <= n; i++) scanf("%d", a + i);
        build(1, 1, n);
        int Q;
        scanf("%d", &Q);
        char op[2];
        int l, r;
        printf("Case #%d:\n", kase++);
        while (Q--) {
            scanf("%s%d%d", op, &l, &r);
            if (op[0] == 'Q')
                printf("%d\n", query(1, 1, n, l, r));
            else
                update(1, 1, n, l, r);
        }
    }
}
```