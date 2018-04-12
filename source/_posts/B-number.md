---
title: B-number
comments: true
date: 2018-04-12 10:14:00
categories:
- ACM
- 动态规划
- 数位dp
---

# 题意
来源： HDU3652

区间内有多少个可以包含“13”，并且可以被13整除的数字。
# 分析
$dp[i][k][sta]$ i表示枚举到第i位，k表示前i位组成的整数模13的结果，sta表示前一位是否为1，以及之前是否出现过13.

这里再解释一下k

假设整数A = （a + b + c + d)

那么A%13 == 0 等价于 (a + b + c + d) % 13 等价于
a % 13 + b % 13 + c % 13 + d % 13

所以最后判断k是否为0，即可知道当前整数是否能被13整除。
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

int num[20];
int dp[20][20][3];

int dfs(int pos, bool limit, int val, int sta) {
    if (pos == -1) return sta == 2 && val == 0;
    if (!limit && dp[pos][val][sta] != -1) return dp[pos][val][sta];
    int up = limit ? pos[num] : 9;
    int ans = 0;
    for (int i = 0; i <= up; i++) {
        if (sta == 2)
            ans += dfs(pos - 1, limit && i == up, (val * 10 + i) % 13, 2);
        else if (i == 1)
            ans += dfs(pos - 1, limit && i == up, (val * 10 + i) % 13, 1);
        else if (sta == 1 && i == 3)
            ans += dfs(pos - 1, limit && i == up, (val * 10 + i) % 13, 2);
        else
            ans += dfs(pos - 1, limit && i == up, (val * 10 + i) % 13, 0);
    }
    if (!limit) dp[pos][val][sta] = ans;
    return ans;
}

int solve(int val) {
    int pos = 0;
    while (val) {
        num[pos++] = val % 10;
        val /= 10;
    }
    return dfs(pos - 1, true, 0, 0);
}
int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);

    int val;
    clr(dp, -1);
    while (cin >> val) {
        cout << solve(val) << endl;
    }
}

```
