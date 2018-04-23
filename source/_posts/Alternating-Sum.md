---
title: Alternating Sum
comments: true
date: 2018-04-18 08:06:34
categories:
- ACM
- 杂题
---

# 题意
来源：[cf 964C](http://codeforces.com/contest/964/problem/C)


You are given two integers a and b. Moreover, you are given a sequence $s_0,s_1,…,s_n.$

All values in s are integers 1 or −1. It's known that sequence is k-periodic and k divides n+1.
In other words, for each k≤i≤n it's satisfied that $s_i=s_i−k.$

Find out the non-negative remainder of division of $\sum^n_{i = 0}s_ia^{n-1}b^i$ by $10^9+9$

Note that the modulo is unusual!

$(1 \leq n \leq 10^{9}, 1 \leq a, b \leq 10^{9}, 1 \leq k \leq 10^{5})$

# 分析

等比数列求和

每个位置的元素，经过一个周期就会变成原来的$a^{-k}b^k$倍，所以可以用等比数列求和。

没有看到题目中说$(n+1) \% k == 0$，所以我的做法是对任何n都适用的。

需要注意的是如何需要判断公比为1的特殊情况（当时直接认为a等于b时才会出现公比为1的情况，事实上因为它不断地在模，所以即使不相等，模完之后也可能相等）

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
const int mod = 1e9 + 9;
ll n, a, b, k;

ll my_pow(ll a, ll b) {
    ll ret = 1;
    while (b) {
        if (b & 1) ret = ret * a % mod;
        a = a * a % mod;
        b >>= 1;
    }
    return ret;
}

ll work(int i) {
    ll m = (n - i) / k + 1;
    // ll m = (n + 1) / k;
    ll ret = 0;
    ll temp = my_pow(b, k) * my_pow(my_pow(a, k), mod - 2) % mod;
    if (temp == 1)
        return m * my_pow(a, n - i) % mod * my_pow(b, i) % mod;  // wa

    temp = (1 - temp + mod) % mod;
    ret = (ret + my_pow(a, n - i) * my_pow(b, i) % mod) % mod;
    ll idx = n - i - m * k;
    if (idx >= 0)
        ret = (ret - my_pow(b, m * k + i) * my_pow(a, idx) % mod + mod) % mod;
    else
        ret = (ret - my_pow(b, m * k + i) * my_pow(my_pow(a, -idx), mod - 2) % mod + mod) % mod;
    ret = ret * my_pow(temp, mod - 2) % mod;
    return ret;
}
int main() {
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    std::ios::sync_with_stdio(false);

    while (cin >> n >> a >> b >> k) {
        string line;
        cin >> line;
        ll ans = 0;
        for (int i = 0; i < line.size() && i <= n; i++) {
            if (line[i] == '+') {
                ans = (ans + work(i)) % mod;
            } else {
                ans = (ans - work(i) + mod) % mod;
            }
        }
        cout << ans << endl;
    }
}

```
