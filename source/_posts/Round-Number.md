---
title: Round-Number
comments: true
date: 2018-04-12 11:16:09
categories:
- ACM
- 动态规划
- 数位dp
---

# 题意
来源： POJ - 3252

一个数其二进制表示形式，若0的数量大于等于1的数量，则称该数合格。

问区间内合格的整数的数量
# 分析
$dp[i][sta]$ i表示枚举到当前位，sta表示到当前位一共有多少个0

因为sta可能会小于零，所以我们先给sta加一个值（offset），最后判断的时候再减去这个offset即可。

这里注意前导零的影响，因为前面的零是不算0的。
# 代码
```cpp
// ybmj
// #include <bits/stdc++.h>
#include <cstring>
#include <iostream>
using namespace std;
#define clr(a, x) memset(a, x, sizeof(a))
#define mp(x, y) make_pair(x, y)
#define pb(x) push_back(x)
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
typedef long long ll;
int num[35];
int dp[35][100];

int dfs(int pos, int sta, bool limit, bool lead) {
    if (pos == -1) return sta >= 35;

    if (!limit && !lead && dp[pos][sta] != -1) return dp[pos][sta];
    int up = limit ? num[pos] : 1;
    int ans = 0;
    for (int i = 0; i <= up; i++) {
        if (lead && i == 0)
            ans += dfs(pos - 1, sta, limit && i == num[pos], true);
        else
            ans += dfs(pos - 1, sta + (i == 0 ? 1 : -1), limit && i == num[pos],
                       false);
    }
    if (!limit && !lead) dp[pos][sta] = ans;
    return ans;
}

int solve(int val) {
    int pos = 0;
    while (val) {
        num[pos++] = val % 2;
        val /= 2;
    }
    return dfs(pos - 1, 35, true, true);
}

int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);
    int l, r;
    clr(dp, -1);
    while (cin >> l >> r) {
        cout << solve(r) - solve(l - 1) << endl;
    }
}

```
