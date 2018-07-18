---
title: NCPC2017-A-Airport Coffee 单调队列优化dp
comments: true
date: 2018-07-15 17:39:53
categories:
- ACM
- 动态规划
- 优化方法
---

## 题意
一个人要走一段长度为l的路,中间有n个咖啡店,给你每个咖啡店的位置.

不喝咖啡时速度为a,喝咖啡时速度为b.

买咖啡不需要时间,买了咖啡之后走t秒后就开始喝咖啡了,每杯咖啡会喝r秒. 最多可以同时拥有一杯咖啡.

问在最短时间内走完这条路需要经过哪些咖啡店?
## 分析
设$dp[i]$为到第i个的咖啡店并买咖啡的最短时间

$$dp[i] = min \{ dp[k] + cost(i,k) \}, k \in [0,i-1]$$

$cost(i,k)$ 有三种情况

$$\begin{cases}
    (\frac{-pos[k]}{a} + dp[k])+ \frac{pos[i]}{a} \quad {a[i] - a[k] < a \times t }\\\\
    (\frac{b\times t - a\times t - pos[k]}{b} + dp[k]) + \frac{pos[i]}{b} \quad {a\times t \leq a[i] - a[k] \leq a\times t + b\times r}\\\\
    (\frac{a\times r - b\times r - pos[k]}{a} + dp[k]) + \frac{pos[i]}{a} \quad {a\times t + b\times r < a[i] - a[k]}
    \end{cases} $$

$$\begin{cases}
        x_1 = x_{1,0} + \frac{a_2}{(a_1,a_2)}\times t\\\\
        {x_2 = x_{2,0} - \frac{a_1}{(a_1,a_2)}\times t}
        \end{cases}$$



发现只要维护三个单调队列即可, 但是实际上只需要维护中间那个.

对于第一种情况,他在上一个店里买了咖啡,到当前店的时候还没开始喝,那么他必然在当前店不会再买咖啡

对于第三种情况,因为咖啡作用完之后还需要再走一段距离,所以这段距离应该越近越好

还是写三个单调队列比较稳一点.


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
typedef pair<double, double> pdd;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 5e5 + 5;
double a[maxn], dp[maxn];
int last[maxn];
int n;

int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    double l, A, B, t, r;
    while (cin >> l >> A >> B >> t >> r) {
        cin >> n;
        a[0] = dp[0] = 0;
        for (int i = 1; i <= n; i++) cin >> a[i];
        a[++n] = l;
        deque<pair<int, double> > dq;  // (pos, val)
        double d = A * t + B * r;
        for (int i = 1, k = 1; i <= n; i++) {
            dp[i] = dp[i - 1] + (a[i] - a[i - 1]) / A;
            last[i] = i - 1;
            while (a[i] - a[k] > d) {
                double foo = (A * r - B * r - a[k] + a[i]) / A + dp[k];
                if (foo < dp[i]) {
                    dp[i] = foo;
                    last[i] = k;
                }
                k++;
            }
            while (dq.size() && a[i] - a[dq.front().first] > d) dq.pop_front();
            if (dq.size()) {
                pair<int,double> u = dq.front();
                if (u.second + a[i] / B < dp[i]) {
                    dp[i] = u.second + a[i] / B;
                    last[i] = u.first;
                }
            }
            double bar = dp[i] + (t * B - t * A - a[i]) / B;
            while (dq.size() && dq.back().second > bar) dq.pop_back();
            dq.push_back(mp(i, bar));
        }
        vector<int> ans;
        for (int i = last[n]; i > 0; i = last[i]) {
            ans.pb(i - 1);
        }
        cout << ans.size() << endl;
        for (int i = ans.size() - 1; i >= 0; i--) cout << ans[i] << ' ';
        cout << endl;
    }
}
```