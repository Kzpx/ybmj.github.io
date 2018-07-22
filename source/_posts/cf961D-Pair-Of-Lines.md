---
title: cf961D Pair Of Lines
date: 2018-04-05 15:55:15
comments: true
categories:
- ACM
- 数学
- 随机概率
---

# 题意
[题目链接](http://codeforces.com/contest/961/problem/D)
给你n个点（二维），问是否能找出两条直线，使得可以覆盖所有点。
# 分析

假设原图满足题目要求。

那么我们每次随机取两个点，将该直线上的点全部去掉， 然后判断剩下的点是否共线。

因为原图满足题目要求，所以我们可以将图中的点分为两个点集。

假设其中一个点集中点的数量为x，则另一个点集中点的数量为y = n-x

那么我们取到的两个点在一个点集中的概率就是$1 - \frac{ C_{x}^{1} \times C_{y}^{1} } { C_{n}^{2}}$

由基本不等式可知，上述概率至少是$\frac{1}{2}$

我们可以随机一百次，这样如果原图是满足题目要求的，那么他成功选取同一个点集中两个点的概率就是$1 - \frac{1}{2^{100}}$.

即，如果原图满足条件，则大概率可以输出yes.

**注意**

每次随机要更换种子！
```cpp
srand(time(0)) // 利用当前系统时间作为种子
```

# 代码
```cpp
// ybmj
#include <bits/stdc++.h>
using namespace std;
#define clr(a, x) memset(a, x, sizeof(a))
#define mp(x, y) make_pair(x, y)
#define pb(x) push_back(x)
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
typedef long long ll;
const int maxn = 1e5 + 5;
const double eps = 1e-9;

pair<ll, ll> p[maxn];

int main() {
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    std::ios::sync_with_stdio(false);

    int n;
    cin >> n;
    for (int i = 0; i < n; i++) {
        cin >> p[i].first >> p[i].second;
    }
    if (n <= 2) {
        cout << "yes" << endl;
        return 0;
    }
    srand(time(0)); //注意！
    bool flag = false;
    for (int i = 0; i < 100; i++) {
        int p1 = rand() % n;
        int p2 = rand() % n;
        while (p1 == p2) p2 = rand() % n;

        ll a = p[p2].first - p[p1].first;
        ll b = p[p2].second - p[p1].second;
        ll m = p[p1].second * p[p2].first - p[p1].first * p[p2].second;

        vector<int> V;
        for (int i = 0; i < n; i++) {
            if (m != p[i].second * a - b * p[i].first) V.push_back(i);
        }
        if (V.size() <= 2) {
            flag = true;
            break;
        }

        p1 = V[0];
        p2 = V[1];
        a = p[p2].first - p[p1].first;
        b = p[p2].second - p[p1].second;
        m = p[p1].second * p[p2].first - p[p1].first * p[p2].second;
        flag = true;
        for (int i = 2; i < V.size(); i++) {
            if (m != p[V[i]].second * a - b * p[V[i]].first) {
                flag = false;
                break;
            }
        }
        if (flag) break;
    }
    if (flag)
        cout << "yes" << endl;
    else
        cout << "no" << endl;
}
```
