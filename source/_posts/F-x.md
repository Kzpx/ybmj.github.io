---
title: F(x)
comments: true
date: 2018-04-12 11:00:37
categories:
- ACM
- 动态规划
- 数位dp
---

# 题意
来源： HDU - 4734

For a decimal number x with n digits $(A_n A_{n-1} ... A_2 A_1)$, we define its weight as $F(x) = A_n \times 2^{n-1} + A_n-1 \times 2^{n-2} + ... + A_1 \times 1$. Now you are given two numbers A and B, please calculate how many numbers are there between 0 and B, inclusive, whose weight is no more than F(A).

# 分析
$dp[i][sta]$ 表示枚举到第i位，还有sta可用。

为什么不用sta表示前i位的和呢？

以为A每次都是不一样的，所以每次都要清空dp数组，会超时的！

**注意**

很重要的技巧！

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
const int maxn = 4700;
ll W[] = {1, 2, 4, 8, 16, 32, 64, 128, 256, 512, 1024};
ll dp[12][maxn];
int num[20];

ll dfs(int pos, bool limit, int sta) {
    if (pos == -1) return 1;
    if (!limit && dp[pos][sta] != -1)
        return dp[pos][sta];  // 表示枚举到pos位，还有sta可用
    int up = limit ? num[pos] : 9;
    ll ans = 0;
    for (int i = 0; i <= up; i++) {
        if (sta - i * W[pos] >= 0) {
            ans += dfs(pos - 1, limit && i == num[pos], sta - i * W[pos]);
        } else
            break;
    }
    if (!limit) dp[pos][sta] = ans;
    return ans;
}

ll solve(ll l, ll r) {
    int pos = 0;
    int val = 0;
    while (l) {
        val += (l % 10) * W[pos++];
        l /= 10;
    }
    pos = 0;
    while (r) {
        num[pos++] = r % 10;
        r /= 10;
    }
    return dfs(pos - 1, true, val);
}
int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);
    int T, kase = 1;
    cin >> T;
    clr(dp, -1);
    while (T--) {
        ll l, r;
        cin >> l >> r;
        cout << "Case #" << kase++ << ": ";
        cout << solve(l, r) << endl;
    }
}

```
