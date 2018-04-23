---
title: Destruction of a Tree
comments: true
date: 2018-04-18 15:46:42
categories:
- ACM
- 杂题
---

# 题意
来源：[cf964d](http://codeforces.com/contest/963/problem/B)

一棵树，可以删去偶度点，问是否能删除掉整棵树？

如果可以则输出删除顺序（spj）

$(1 ≤ n ≤ 2·10^5)$
# 分析
递归

最近写不出来题目了，简单的递归瞎搞都写不来了。

对每个点，判断其度的奇偶（通过对未删除的边进行计数），若是偶数点则删除以该点为根的整棵子树。

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
const int maxn = 2e5 + 5;
vector<int> G[maxn];
vector<int> ans;
bool vis[maxn];
void addedge(int u, int v) {
    G[u].pb(v);
    G[v].pb(u);
}

void remove(int u, int fa) {
    if (vis[u]) return;
    vis[u] = true;
    ans.pb(u);
    for (auto v : G[u]) {
        if (v == fa) continue;
        remove(v, u);
    }
}

void dfs(int u, int fa) {
    int deg = fa == -1 ? 0 : 1;
    for (auto v : G[u]) {
        if (v == fa) continue;
        dfs(v, u);
        if (!vis[v]) deg ^= 1;
    }
    if (!deg) remove(u, fa);
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    std::ios::sync_with_stdio(false);
    int n;
    while (cin >> n) {
        for (int i = 1; i <= n; i++) G[i].clear();
        clr(vis, 0);
        ans.clear();
        for (int i = 1; i <= n; i++) {
            int v;
            cin >> v;
            if (v != 0) addedge(i, v);
        }
        dfs(1, -1);
        if (ans.size() != n)
            cout << "NO" << endl;
        else {
            cout << "YES" << endl;
            for (int i = 0; i < ans.size(); i++) cout << ans[i] << endl;
        }
    }
}
```
