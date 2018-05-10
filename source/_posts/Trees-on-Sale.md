---
title: Trees on Sale
comments: true
date: 2018-05-08 20:50:38
categories:
- ACM 
- 杂题
---

# 题意
来源:[第十四届华中科技大学程序设计竞赛决赛B](https://www.nowcoder.com/acm/contest/119/B)

给定N，K $(1 \leq N \leq 10^5, 1 \leq K \leq 10^{12})$

我是一个花店老板，我每次营业之前都会将花打包起来，每一束最少有一枝花。 

已知每日都会有N个人来买花，每个人最多一次买K枝花，问我最少需要包几束花。

可以将若干束花合成一束，但不能把一束拆开。

# 分析

假如N个人都买一枝花，那么我们需要N束只含一枝花的。

假如N个人都买两枝花，那么我们可以利用之前的N束花， 所以我们只需要 N - N/2

假如N个人都买三枝花，那么我们可以利用之前的N + N*2 束花， 所以我们只需要
N - (N + N * 2) / 3

但是这样下去是O(K)的，不行。

我们可以发现这么一个通式： 

第i枝花需要: max(0, N - (preSum) / i) 

所以我们可以通过 preSum / i + 1,来跳到下一个需要计算的。
# 代码
```cpp
//ybmj
#include<bits/stdc++.h>
using namespace std;
#define lson (rt << 1)
#define rson (rt << 1 | 1)
#define lson_len (len - (len >> 1))
#define rson_len (len >> 1)
#define pb(x) push_back(x)
#define clr(a, x) memset(a, x, sizeof(a))
#define mp(x, y) make_pair(x, y)
typedef long long ll;
typedef pair<int,int> pii;
typedef pair<ll,ll> pll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
 
int main(){
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    std::ios::sync_with_stdio(false);
    ll n,k;
    while(cin >> n >> k){
        ll sum = 0;
        ll ans = 0;
        ll i = 1;
        while(i <= k){
            ll cnt = n - sum / i;
            sum += cnt * i;
            ans += cnt;
            i = sum / n + 1;
        }
        cout << ans << endl;
    }
}
```
