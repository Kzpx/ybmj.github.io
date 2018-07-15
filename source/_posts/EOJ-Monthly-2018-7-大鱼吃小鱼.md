---
title: EOJ Monthly 2018.7 大鱼吃小鱼
comments: true
date: 2018-07-12 09:29:49
categories:
- ACM
- 贪心
---

## 题意
有n条鱼

1. 任意时刻只能吃大小小于等于自己的鱼。
2. 任何时刻都要保证鱼的大小是正整数。
3. 当吃了第 i 条鱼之后自己的大小 x 会变为 $x+a_i$。

初始鱼的大小至少需要多大才能吃完这 n 条鱼?

吃的顺序可以任意。

## 分析
二分最后答案,然后先吃掉所有$a_i >= 0$的鱼

对于$a_i<0$的鱼, 有两种方法:
1. 考虑吃掉i之前,$x\geq w_i$,吃掉i之后,$x \geq w_i + a_i$, 要使体型尽可能的大,所以按$a+w$排序即可.
2. 先把所有鱼都吃掉,然后看如果$x \geq w_i + a_i$, 那么就减去$a_i$,也是按$a+w$排序即可.
## 代码
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
typedef long long ll;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
const ll INF = 1e18;
const int NINF = 0xc0c0c0c0;
struct P {
    ll w, a;
};
vector<P> foo, bar;
const int maxn = 1e6 + 5;
bool vis[maxn];
bool ok(ll x) {
    for (auto &v : foo)
        if (x >= v.w)
            x += v.a;
        else
            return false;
    for (auto &v : bar) {
        if (x >= v.w)
            x -= v.a;
        else
            return false;
    }

    if (x <= 0) return false;
    return true;
}
int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);

    int n;
    while (cin >> n) {
        int u, v;
        foo.clear();
        bar.clear();
        temp.clear();
        for (int i = 0; i < n; i++) {
            cin >> u >> v;
            if (v >= 0) {
                foo.push_back({u, v});
            } else {
                bar.push_back({u, -v});
                // temp.push_back({u, -v});
            }
        }
        for (int i = 0; i < bar.size(); i++) bar[i].id = i;
        // for (int i = 0; i < temp.size(); i++) temp[i].id = i;
        sort(foo.begin(), foo.end(), cmp1);
        sort(bar.begin(), bar.end(), cmp2);
        // sort(temp.begin(), temp.end(), cmp3);
        ll l = 1, r = 1e18;
        ll ans;
        while (l <= r) {
            ll mid = l + r >> 1;
            if (ok(mid)) {
                ans = mid;
                r = mid - 1;
            } else
                l = mid + 1;
        }
        cout << ans << endl;
    }
}
```
