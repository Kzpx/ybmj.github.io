---
title: bzoj1492-Cash-CDQ分治入门题
comments: true
date: 2018-09-05 23:20:44
categories:
- ACM
- 分治
---

## 题意
股市中有两种金券A和B，每天都会有个成交价格$a_i,b_i$

每天可以做无限次操作。

操作分为两类：

1. 按当天固定比例$c_i$购买金券。 即每天都会有个比例$c_i$，若要买k元的金卷，那么保证购入的A卷的数量比B卷的数量等于$c_i$
2. 按任意相同比例卖出金卷，即同时要卖出百分之x的金卷。

如果你一开始有S元，那么请问n天后，最多能获得多少钱？

拥有的卷的数量为实数。

$1 \leq n \leq 1e5$
## 分析

$dp[i]$:表示第i天所能获得的最大金钱。

$x[i]$表示第i天最多能获得的A卷数量， $y[i]$表示第i天最多能获得的B卷数量

$$dp[i] = Max(x[j] * a[i] + y[j] * b[i]), j \in [1,i-1]$$

如果决策k优于决策j，那么

$$(x[j] - x[k])a[i] < (y[k] - y[j])b[i]$$

不妨设$x[k] > x[j]$,那么

$$\frac{y[k] - y[j]}{x[k] - x[j]} > -\frac{a[i]}{b[i]}$$

因此根据斜率优化dp的知识，我们知道需要维护一个$x[i]$递增的，并且斜率递减的队列。 

同时由于不等式右边并没有单调性，因此我们需要在队列里二分。 但这个队列有点难维护（貌似还要手写平衡树 （ 我会补的！ ） 因此我们的CDQ分治就要登场啦！

---

定义$solve(l,r)$是计算出$[l,r]$的答案(dp值)。

那么我先计算$solve(l,mid)$

然后用$[l,mid]$的结果算一下对$[mid+1,r]$的贡献

然后再计算$solve(mid+1,r)$

是不是就是$solve(l,r)$了呢？

（这大概就是cdq分治了

那么$[l,mid]$对$[mid+1,r]$的贡献怎么算呢？

我们将$[l,mid]$的元素按$x$排序，保证了$x$的递增

然后将$[mid+1,r]$的元素按$-\frac{a[i]}{b[i]}$从小到大排序，这样保证了不等式右边的单调性。

这样的话我们就可以用单调队列优化了！ 维护一个斜率递减的单调队列，$O(nlogn)$即可解决问题。

---
看了一下网上别人的代码，发现还可以优化。 不需要排序，可以将$O(nlogn)$降为$O(n)$

一开始就将数组按$-\frac{a[i]}{b[i]}$排序， 然后在$solve(mid+1,r)$解决之后，再将$[l,r]$按$x$从小到大排序。




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
const double eps = 1e-8;
const int maxn = 1e5 + 5;
struct P {
    double a, b, r, k, x, y;
    int id;
    bool operator<(const P &A) const { return k > A.k; }
};
P p[maxn], cpy[maxn];
double dp[maxn];
int q[maxn];
inline double slope(int j, int k) {
    if (fabs(p[j].x - p[k].x) < eps) {
        if (p[k].y - p[j].y > 0)
            return 1e10;
        else
            return -1e9;
    }
    return (p[j].y - p[k].y) / (p[j].x - p[k].x);
}
void solve(int l, int r) {
    if (l == r) {
        dp[l] = max(dp[l - 1], dp[l]);
        p[l].y = dp[l] / (p[l].a * p[l].r + p[l].b);
        p[l].x = p[l].y * p[l].r;
        return;
    }
    int m = l + r >> 1;
    int l1 = l, l2 = m + 1;
    for (int i = l; i <= r; i++) {
        if (p[i].id <= m)
            cpy[l1++] = p[i];
        else
            cpy[l2++] = p[i];
    }
    for (int i = l; i <= r; i++) p[i] = cpy[i];
    solve(l, m);
    int L = l, R = l - 1;
    for (int i = l; i <= m; i++) {
        while (L < R && slope(q[R - 1], q[R]) < slope(q[R], i)) R--;
        q[++R] = i;
    }
    for (int i = m + 1, id; i <= r; i++) {
        while (L < R && slope(q[L], q[L + 1]) > p[i].k) L++;
        id = q[L];
        dp[p[i].id] = max(dp[p[i].id], p[id].x * p[i].a + p[id].y * p[i].b);
    }
    solve(m + 1, r);
 
    l1 = l, l2 = m + 1;
    int cur = l;
    while (l1 <= m && l2 <= r) {
        if (p[l1].x < p[l2].x + eps)
            cpy[cur++] = p[l1++];
        else
            cpy[cur++] = p[l2++];
    }
    while (l1 <= m) cpy[cur++] = p[l1++];
    while (l2 <= r) cpy[cur++] = p[l2++];
    for (int i = l; i <= r; i++) p[i] = cpy[i];
}
int main() {
    //  /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    //  */
    std::ios::sync_with_stdio(false);
    int n;
    double s;
    scanf("%d%lf", &n, &s);
    for (int i = 1; i <= n; i++) {
        scanf("%lf%lf%lf", &p[i].a, &p[i].b, &p[i].r);
        p[i].id = i;
        p[i].k = -p[i].a / p[i].b;
    }
    sort(p + 1, p + 1 + n);
    dp[0] = s;
    solve(1, n);
    printf("%.3lf\n", dp[n]);
}
```