---
title: K序列
comments: true
date: 2018-04-16 14:53:44
categories:
- ACM
- 杂题
---
# 题意
来源：[shuoj517](http://acmoj.shu.edu.cn/problem/517/)

给一个数组a，长度为n，若某个子序列中的和为K的倍数，那么这个序列被称为“K 序列”。现在要你对数组a 求出最长的子序列的长度，满足这个序列是K 序列。

$1≤n≤10^5,1≤a[i]≤10^9, 1≤nK^2≤10^7$
# 分析

小数据的话用二维dp

$dp[i][k]$ 表示前i个元素，和的余数为k，的最长子序列的长度

转移方程：
$dp[i][(k + a[i])\% K] = max(dp[i-1][(k+a[i])\%K],dp[i-1][k]+1)$

但是由于题目数据很大，所以我们需要滚动第一维，所以递推方程变为：

$dp[i \& 1][(k + a[i]) \% K]
= max(dp[(i \& 1) \bigoplus 1][(k +  a[i]) \% K],dp[(i \& 1) \bigoplus 1][k] + 1)$

一开始想到了二维dp，但是因为对滚动反应不够，所以就放弃了那个想法。
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
const int maxn = 1e5 + 5;
int a[maxn];
int dp[2][maxn];

int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);
    int n, K;
    while (cin >> n >> K) {
        for (int i = 1; i <= n; i++) {
            cin >> a[i];
            a[i] %= K;
        }
        clr(dp, 0xc0);
        dp[0][0] = 0;
        // dp[i][k] 前i个，余数为k，的最长子序列长度

        for (int i = 1; i <= n; i++) {
            for (int k = 0; k < K; k++) {
                dp[i & 1][(k + a[i]) % K] = max(dp[(i & 1) ^ 1][(k + a[i]) % K],
                                                dp[(i & 1) ^ 1][k] + 1);
                // dp[i][(k + a[i]) % K] =
                // max(dp[i - 1][(k + a[i]) % K], dp[i - 1][k] + 1);
            }
        }
        /*
        for (int i = 1; i <= n; i++) {
            for (int k = 0; k < K; k++) {
                cout << dp[i][k] << ' ';
            }
            cout << endl;
        }
        */
        cout << dp[n & 1][0] << endl;
    }
}
```
