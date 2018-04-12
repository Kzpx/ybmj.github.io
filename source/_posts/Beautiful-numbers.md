---
title: Beautiful-numbers
comments: true
date: 2018-04-12 10:43:33
categories:
- ACM
- 动态规划
- 数位dp
---

# 题意
来源：CodeForces - 55D

每个整数可以被他每个非零的数位整除。

问区间内符合上述条件的整数的个数。
# 分析
被每个非零数位整除，即可以被所有非零数位的最小公倍数整除。

所以我们可以枚举这个最小公倍数，1~9的最小公倍数为2520.

所以我们可以得到

$dp[i][k][sta]$ i表示枚举到当前位，k表示所有数位的最小公倍数，sta表示当前整数模k的结果。

最后如果sta为0，则return 1

但是！ 爆内存了！

考虑我们要枚举的是最小公倍数，但之前上我们做的是枚举1 到 2520，然而实际上真正的最小公倍数实际上不过只有40多个。（如果2520能被k整除，那么k一定是一个由若干数位组成的最小公倍数）

所以我们用一个map映射一下，就可以减少内存的消耗啦！
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
const int maxn = 2600;
ll dp[20][50][maxn];
int num[20];
map<int, int> maps;

ll gcd(ll a, ll b) {
    if (b)
        while ((a %= b) && (b %= a))
            ;
    return a + b;
}
ll lcm(ll a, ll b) {
    if (a == 0 || b == 0) return a + b;
    return a * b / gcd(a, b);
}

ll dfs(int pos, bool limit, int sta, int val) {
    if (pos == -1) {
        if (val % sta == 0) {
            return 1;
        } else
            return 0;
    }
    if (!limit && dp[pos][maps[sta]][val] != -1) return dp[pos][maps[sta]][val];

    int up = limit ? num[pos] : 9;
    ll ans = 0;
    for (int i = 0; i <= up; i++) {
        ans += dfs(pos - 1, limit && i == num[pos], lcm(sta, i),
                   (val * 10 + i) % 2520);
    }
    if (!limit) dp[pos][maps[sta]][val] = ans;
    return ans;
}

ll solve(ll val) {
    int pos = 0;
    while (val) {
        num[pos++] = val % 10;
        val /= 10;
    }
    return dfs(pos - 1, true, 1, 0);
}
int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);
    int cnt = 0;
    for (int i = 1; i <= 2520; i++) {
        if (2520 % i == 0) {
            maps[i] = cnt++;
        }
    }
    int T;
    cin >> T;
    clr(dp, -1);
    while (T--) {
        ll l, r;
        cin >> l >> r;
        cout << solve(r) - solve(l - 1) << endl;
    }
}
```
