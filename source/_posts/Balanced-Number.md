---
title: Balanced-Number
comments: true
date: 2018-04-12 10:25:10
categories:
- ACM
- 动态规划
- 数位dp
---

# 题意
来源： HDU3709

一个数被称作平衡数当且仅当其存在一个对称轴，使得对称轴两边可以平衡。

定义重量为数位的值乘数位到对称轴的距离。

比如4139

可以把3当做对称轴，4 * 2 + 1 * 1 == 9 * 1

问区间内有多少个平衡数

# 分析

枚举对称轴和对称轴左边的值

$dp[i][k][sta]$ i表示枚举到第i位，k表示对称轴的位置，sta表示枚举到当前位的值。

最后sta为0时表示当前整数符合条件，return 1.

这里如果sta变成负数，就不需要继续dfs下去了。

还要注意的是左边界为0，所以需要做一下特殊情况的判断。
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
ll dp[20][20][2000];

ll dfs(int pos, bool limit, int mid, int w) {
    if (pos == -1) return w == 0;
    if (!limit && dp[pos][mid][w] != -1) return dp[pos][mid][w];
    int up = limit ? num[pos] : 9;
    ll ans = 0;
    for (int i = 0; i <= up; i++) {
        if (pos > mid) {
            ans += dfs(pos - 1, limit && i == up, mid, w + i * (pos - mid));
        } else if (pos < mid) {
            ans += dfs(pos - 1, limit && i == up, mid, w - i * (mid - pos));
        } else
            ans += dfs(pos - 1, limit && i == up, mid, w);
    }
    if (!limit) dp[pos][mid][w] = ans;
    return ans;
}

ll solve(ll val) {
    if (val == -1) return 0;
    if (val == 0) return 1;
    int pos = 0;
    while (val) {
        num[pos++] = val % 10;
        val /= 10;
    }
    ll ans = 0;
    for (int i = 0; i < pos; i++) {
        ans += dfs(pos - 1, true, i, 0);
    }
    return ans;
}

int countnum(ll val) {
    int cnt = 0;
    if (val < 10) {
        cnt = 1;
    } else {
        while (val) {
            cnt++;
            val /= 10;
        }
    }
    return cnt;
}
int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    */
    std::ios::sync_with_stdio(false);
    int T;
    cin >> T;
    clr(dp, -1);
    while (T--) {
        ll l, r;
        cin >> l >> r;
        ll ans = solve(r) - solve(l - 1);
        int cntr = countnum(r);
        int cntl = countnum(l - 1);
        cout << ans - (cntr - cntl) << endl;
    }
}

```
