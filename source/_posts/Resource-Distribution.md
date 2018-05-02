---
title: Resource Distribution
comments: true
date: 2018-05-02 23:14:35
categories:
- ACM
- 杂题
---

# 题意
来源:[cd967d](http://codeforces.com/contest/925/problem/B)

给n个服务器，每个服务器都有一个负载。

现在要组建两个大型服务器S1和S2，要求S1能负载x1，S2能负载x2.

每个大型服务器的负载都会平均分配到每个小服务器上。

每个服务器最多服务一个大型服务器。

问是否有满足条件的分配方案

$2 \leq n \leq 300\,000, 1 \leq x_1, x_2 \leq 10^9$
# 分析

有一个显而易见的性质：能选大的一定不选小的。

所以在数组排好序后，最后的结果一定是一段包含右端点的连续区间，现在问题就是S1和S2的次序问题，都枚举一下，有一个解就是yes。

一开始想着二分k，但是后来发现这个不满足单调的性质。赛后看了别人代码才明白。
# 代码
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
#define pii pair<int, int>
typedef long long ll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 3e5 + 5;
pii c[maxn];
int x[2];
vector<int> ans[2];

bool work(int i1,int i2,int n){
    int s = -1;
    for(int i=0;i<n;i++){
        if(c[i].first * (i + 1) >= x[i1]){
            s = i;
            break;
        }
    }
    if(s == -1) return false;
    for(int i=s+1;i<n;i++){
        if(c[i].first * (i - s) >= x[i2]){
            for(int k=0;k<=s;k++) ans[i1].pb(c[k].second);
            for(int k=s+1;k<=i;k++) ans[i2].pb(c[k].second);
            return true;
        }
    }
    return false;
}

int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);

    int n;
    while (cin >> n >> x[0] >> x[1]) {
        for(int i=0;i<2;i++) ans[i].clear();
        for (int i = 0; i < n; i++) {
            cin >> c[i].first;
            c[i].second = i + 1;
        }
        sort(c, c + n, greater<pii>());
        if (work(0, 1, n) || work(1, 0, n)) {
            cout << "Yes" << endl;
            cout << ans[0].size() << ' ' << ans[1].size() << endl;
            for (int i = 0; i < 2; i++) {
                for (auto I : ans[i]) cout << I << ' ';
                cout << endl;
            }
        } else
            cout << "No" << endl;
    }
}
```
