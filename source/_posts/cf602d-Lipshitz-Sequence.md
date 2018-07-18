---
title: cf602d Lipshitz Sequence
comments: true
date: 2018-06-29 17:38:56
categories:
- ACM
- 数据结构
- 单调栈
---

来源: [cf 602d](http://codeforces.com/problemset/problem/602/D)


## 分析
相邻两个元素的L(h)是最大的。

单调栈预处理每个区间所覆盖的区间即可。

注意要[l,r)　左开右闭，否则会重复计算元素
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
#define my_unique(a) a.resize(distance(a.begin(), unique(a.begin(), a.end())))
#define my_sort_unique(c) (sort(c.begin(), c.end())), my_unique(c)
typedef long long ll;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e5 + 5;
int a[maxn];
int L[maxn], R[maxn];

void init(int n) {
    stack<pii> st;
    for (int i = 1; i < n; i++) {
        while (st.size() && (abs(a[i] - a[i - 1])) >= st.top().second) st.pop();
        if (st.empty())
            L[i] = 0;
        else
            L[i] = st.top().first;
        st.push(mp(i, abs(a[i] - a[i - 1])));
    }
    while (!st.empty()) st.pop();
    for (int i = n - 1; i > 0; i--) {
        while (st.size() && (abs(a[i] - a[i - 1])) > st.top().second) st.pop();
        if (st.empty())
            R[i] = n - 1;
        else
            R[i] = st.top().first - 1;
        st.push(mp(i, abs(a[i] - a[i - 1])));
    }
}

int main() {
// /*
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    // */
    std::ios::sync_with_stdio(false);
    int n, q;
    while (cin >> n >> q) {
        for (int i = 0; i < n; i++) cin >> a[i];
        init(n);
        int l, r;
        ll ans;
        for (int i = 0; i < q; i++) {
            cin >> l >> r;
            ans = 0;
            for (int k = l; k < r; k++) {
                int ri = min(R[k], r-1);
                int li = max(L[k], l-1);
                ans += 1LL * (ri - k + 1) * (k - li) * abs(a[k] - a[k - 1]);
            }
            cout << ans << endl;
        }
    }
}
```