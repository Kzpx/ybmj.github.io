---
title: Balanced-Number-spoj
comments: true
date: 2018-04-12 10:35:12
categories:
- ACM
- 动态规划
- 数位dp
---

# 题意
来源：Cuban Olympiad in Informatics 2012 - Day 2 Problem A

数位的值为偶数，则要出现奇数次。

数位的值为奇数，则要出现偶数次。

计算区间内符合条件的整数的数量。

# 分析
首先因为数位的值一共只有10个(0 ~ 9),所以我们可以用二进制来保存每个值出现的次数是奇是偶（0 表示出现偶数次，1 表示出现奇数次）

但有个问题是，如果某个值在整数中没有出现过，那么它并不能直接判定为0.

也就是说我们需要一个vis，来表示某个值（0 ～ 9）是否在当前整数中出现过。

我的解决办法是再用十位二进制数来记录，如果值出现过，则相应的二进制为1.

在check的时候，先检查当前数位是否出现过，再检查出现次数的奇偶性。

$dp[i][sta]$ i表示枚举到当前位，sta表示当前状态。（高十位表示vis，低十位表现出现次数的奇偶性）

**注意**

这种用二进制保存状态的方法非常常见！
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
typedef unsigned long long ull;
const int maxn = 1e6 + 5e4;
int num[25];
ll dp[25][maxn];

bool check(int sta) {
    for (int i = 10; i < 20; i++) {
        if ((sta >> i) & 1) {
            if ((i & 1) == ((sta >> (i - 10)) & 1)) return false;
        }
    }
    return true;
}
int getsta(int sta, int val) {
    sta |= (1 << (val + 10));
    sta ^= (1 << val);
    return sta;
}
ll dfs(int pos, bool limit, bool lead, int sta) {
    if (pos == -1) return check(sta);
    if (!limit && !lead && dp[pos][sta] != -1) return dp[pos][sta];
    int up = limit ? num[pos] : 9;
    ll ans = 0;
    for (int i = 0; i <= up; i++) {
        if (lead && i == 0)
            ans += dfs(pos - 1, limit && i == up, true, sta);
        else
            ans += dfs(pos - 1, limit && i == up, false, getsta(sta, i));
    }
    if (!limit && !lead) dp[pos][sta] = ans;
    return ans;
}

ll solve(ll val) {
    int pos = 0;
    while (val) {
        num[pos++] = val % 10;
        val /= 10;
    }
    return dfs(pos - 1, true, true, 0);
}

int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);
    int T;
    cin >> T;
    clr(dp, -1);
    while (T--) {
        ull l, r;
        cin >> l >> r;
        cout << solve(r) - solve(l - 1) << endl;
    }
}

```
