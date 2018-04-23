---
title: ultmaster 的小迷妹们
comments: true
date: 2018-04-23 10:59:14
categories:
- ACM
- 杂题
---

# 题意
来源:[eoj2018.4.A](https://acm.ecnu.edu.cn/contest/69/problem/A/)

给你一个$n\times n$的正方形，和若干$x\times y$的长方形，问你是否能拼成一个更大的正方形。

$n,x,y (1≤n,x,y≤10^9)$
# 分析
我们可以把小正方形放到中心位置。

可以发现整个大的正方形可以分成四个对称的部分（除去中心的小正方形）

可以得到$\frac{n + k_1x}{y} = k_2$ 或者 $\frac{n + k_2y}{x} = k_1$  

即$k_2y - k_1x = n$ 或者 $k_1x - k_2y = n$

x，y要取正数解。即只要这个二元一次方程有解即可。


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

int gcd(int a, int b) {
    if (b)
        while ((a %= b) && (b %= a))
            ;
    return a + b;
}

int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);
    int n, x, y;
    while (cin >> n >> x >> y) {
        int a = min(x, y);
        int b = max(x, y);
        if (n % gcd(a, b) == 0)
            cout << "Yes" << endl;
        else
            cout << "No" << endl;
    }
}
```
