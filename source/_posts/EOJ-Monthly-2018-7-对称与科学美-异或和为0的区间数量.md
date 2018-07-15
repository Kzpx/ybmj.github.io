---
title: EOJ Monthly 2018.7 对称与科学美 异或和为0的区间数量
comments: true
date: 2018-07-12 09:20:16
categories:
- ACM
- 杂题
---

## 题意

给一个数列，问有多少连续子数列，符合对称与科学美，也就是每个数字的出现次数均为偶数。

[题目](https://acm.ecnu.edu.cn/contest/92/problem/E/)

## 分析

满足题意的区间，其异或和为0，反之不一定成立。

所以我们可以对每个相同的数字重新随机一个新的数字，然后再计算答案

多次随机，在所有答案中取最小的即可。


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
const int maxn = 4e5;
map<ll, ll> cnt;
ll back[maxn], a[maxn];
vector<ll> G[maxn];

int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    int n;
    srand(time(0));
    while (cin >> n) {
        for (int i = 1; i <= n; i++) {
            cin >> a[i];
        }
        ll ans = 1e18;
        for (int i = 1; i <= 10; i++) {
			ll t = rand();
            for (int k = 1; k <= n; k++) a[k] *= t;
            cnt.clear();
            ll x = 0;
            ll foo = 0;
            for (int k = 1; k <= n; k++) {
                x ^= a[k];
                foo += cnt[x] + (x == 0);
                cnt[x]++;
            }
            ans = min(ans, foo);
        }
        cout << ans << endl;
    }
}
```
