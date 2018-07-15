---
title: cf981e Addition on Segments
comments: true
date: 2018-07-15 15:12:34
categories:
- ACM
- 数据结构
- Bitset
---

## 题意
给你一个长度为n的全为0的序列,接下来有m个区间加的操作,你可以从这m个操作中任取一些操作实施,然后会得到
一个区间最大值. 问区间最大值在1到n的可能的情况有多少个?

$1 \leq n ,q \leq 10^4 $
## 分析
我们可以枚举位置i的值是最大值, 这样对于不包括i的操作就可以舍去. 然后看i有多少种取值是在1到n之间的.

对于区间加的操作我们可以用线段树的lazy来维护, 这样我们在查询i的值时那些不包括i的操作就会被舍去.

然后就是对每个点的lazy进行01背包,即每个区间操作取或不取. (每个节点的lazy不只是一个值,因为可能有多个区间覆盖一个点的情况)

因为最大值的区间在1到n,所以我们可用bitset进行优化,最后复杂度为$O(n^2logn / 64)$
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
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e4 + 5;
vector<int> lazy[maxn << 2];
bitset<maxn> dp[100], ans;
void update(int l, int r, int rt, int L, int R, int x) {
    if (L <= l && R >= r) {
        lazy[rt].pb(x);
        return;
    }
    int m = l + r >> 1;
    if (L <= m) update(l, m, lson, L, R, x);
    if (m < R) update(m + 1, r, rson, L, R, x);
}

void query(int l, int r, int rt, int k) {
    dp[k] = dp[k - 1];
    for (auto &v : lazy[rt]) dp[k] |= dp[k] << v;
    if (l == r) {
        ans |= dp[k];
        return;
    }
    int m = l + r >> 1;
    query(l, m, lson, k + 1);
    query(m + 1, r, rson, k + 1);
}

int main() {
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);
    bitset<maxn> bt, foo;
    int n, m;
    while (cin >> n >> m) {
        for (int i = 0; i < 2 * n + 5; i++) {
            lazy[i].clear();
        }
        int l, r, x;
        for (int i = 0; i < m; i++) {
            cin >> l >> r >> x;
            update(1, n, 1, l, r, x);
        }
        dp[0].set(0);
        ans.reset();
        query(1, n, 1, 1);
        int cnt = 0;
        for (int i = 1; i <= n; i++) {
            cnt += ans[i];
        }
        cout << cnt << endl;
        for (int i = 1; i <= n; i++) {
            if (ans[i] == 1) cout << i << ' ';
        }
        cout << endl;
    }
}
```
