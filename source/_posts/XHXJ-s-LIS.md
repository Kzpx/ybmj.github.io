---
title: XHXJ's-LIS
comments: true
date: 2018-04-12 11:20:27
categories:
- ACM
- 动态规划
- 数位dp
---

# 题意
来源：HDU - 4352

一个整数，他的数位的最长上升子序列的长度为k，则成是合格的。

问区间内合格的整数的数量
# 分析
因为数位的取值一共只有十个（0~9）， 所以我们可以用十个二进制来保存状态。

0表示出现过，1表示未出现过

最后check的时候检查状态中1的数量是否为k即可。

$dp[i][sta][k]$ i 表示枚举第i位，sta表示当前状态，k表示最长上升子序列的长度。

这里需要注意的是更新状态的方法。

做法与nlog(n)求最长上升子序列的思想相同。

假设要更新第pos位，那么就找原状态中第一个大于等于pos位置，然后将其置为0！ 然后把pos位设为1.

如果想不通就好好想想是怎么处理最长上升子序列的吧。
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
ll dp[20][1024][20];
int num[20];
int w[20];
int K;

int getnew(int sta, int val) {
    for (int i = val; i < 10; i++) {
        if (sta & (1 << i))
            return (sta ^ (1 << i)) | (1 << val);  // !求最长上升子序列！
    }
    // sta ^= 1 << (val);
    sta |= 1 << (val);
    return sta;
}
int getnum(int sta) {
    int ret = 0;
    while (sta) {
        if (sta & 1) ret++;
        sta >>= 1;
    }
    return ret;
}
ll dfs(int pos, bool limit, bool lead, int sta) {
    if (pos == -1) return getnum(sta) == K;
    if (!limit && !lead && dp[pos][sta][K] != -1) return dp[pos][sta][K];
    int up = limit ? num[pos] : 9;
    ll ans = 0;
    for (int i = 0; i <= up; i++) {
        if (lead && i == 0)
            ans += dfs(pos - 1, limit && i == up, lead && i == 0, sta);
        else
            ans +=
                dfs(pos - 1, limit && i == up, lead && i == 0, getnew(sta, i));
    }
    if (!limit && !lead) dp[pos][sta][K] = ans;
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

    int T, kase = 1;
    cin >> T;
    clr(dp, -1);
    while (T--) {
        cout << "Case #" << kase++ << ": ";
        ll l, r;
        cin >> l >> r >> K;
        cout << solve(r) - solve(l - 1) << endl;
    }
}

```
