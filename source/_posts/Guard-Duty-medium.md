---
title: Guard Duty (medium)
comments: true
date: 2018-04-16 10:21:55
categories:
- ACM
- 杂题
---
# 题意

来源：[cd 958 e2](http://codeforces.com/contest/958/problem/E2)

从一个数组中选Ｋ个不相邻的数，使得和最小。

K, N (2 ≤ 2K ≤ N ≤ 500000, K ≤ 5000)
# 分析

对于小数据，我们可以用ｄｐ做。

$dp[i][k][vis]$ 表示从前ｉ个里面选ｋ个，ｖｉｓ表示第ｉ位有没有选。

转移方程:　$$dp[i][k][0] = min(dp[i-1][k][0],dp[i-1][k][1])$$

$$dp[i][k][1] = min(dp[i-1][k-1][0],dp[i-1][k-1][1]) + cost[i]$$

对于原题中的数据就不能这么做啦。

最朴素的想法是每次取最小的元素，但是这么贪心明显是错误的，因为最小的元素不一定在最终的答案里。

为什么最小的元素不一定在最终的答案里呢？

>a[] = {4 ,1, 5, 100};

我们会先取1，然后再取100,。 但是4 + 5 < 1 + 100

所以我们在贪心的时候，每取一个数，则要把他相应的“补救措施”也放进这个贪心的过程中。

所谓的补救措施就是：不取当前元素，而取其相邻的两个元素。

这里有个性质： 如果4 或 5其中一个在最后的答案里，那么这个答案一定不是最优的，因为我可以将其替换成1，而不影响其它元素的选取。

为什么是两个？ 看上面的例子，当我们取第二个元素时，我们取到了100。 但如果考虑其”补救措施“，那么我们取到的就应该是 4 + 5 - 1.

这里因为有元素的删除，我们采用静态双向链表实现。


# 代码
```cpp
// ybmj
#include <bits/stdc++.h>
using namespace std;
#define clr(a, x) memset(a, x, sizeof(a))
#define mp(x, y) make_pair(x, y)
#define pb(x) push_back(x)
const int INF = 0x7fffffff;
const int NINF = 0xc0c0c0c0;
typedef long long ll;
const int maxn = 5e5 + 5;
ll a[maxn], d[maxn], pre[maxn], nxt[maxn];
bool vis[maxn];

int main() {
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    std::ios::sync_with_stdio(false);
    int k, n;
    while (cin >> k >> n) {
        for (int i = 0; i < n; i++) cin >> a[i];
        sort(a, a + n);
        priority_queue<pair<ll, int>, vector<pair<ll, int> >,
                       greater<pair<ll, int> > >
            pq;
        for (int i = 1; i < n; i++) {
            d[i] = a[i] - a[i - 1];
            pre[i] = i - 1;
            nxt[i] = i + 1;
            vis[i] = 0;
            pq.push(mp(d[i], i));
        }
        d[0] = d[n] = INF;
        ll ans = 0;
        for (int i = 0; i < k; i++) {
            while (true) {
                if (pq.empty()) break;
                int i = pq.top().second;
                pq.pop();
                if (vis[i]) continue;
                ans += d[i];
                d[i] = -d[i];

                vis[pre[i]] = true;
                d[i] += d[pre[i]];
                pre[i] = pre[pre[i]];
                nxt[pre[i]] = i;
                vis[nxt[i]] = true;
                d[i] += d[nxt[i]];
                nxt[i] = nxt[nxt[i]];
                pre[nxt[i]] = i;
                pq.push(mp(d[i], i));
                break;
            }
        }
        cout << ans << endl;
    }
}

```
