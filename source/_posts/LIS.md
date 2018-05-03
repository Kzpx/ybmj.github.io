---
title: LIS
comments: true
date: 2018-05-03 22:53:39
categories:
- ACM
- 杂题
---

# 题意
来源:[zoj4028](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemId=5769)

构造一个长度为n的序列，满足以下条件：

f[i] 表示以第i个元素结尾的最长上升子序列的长度。

l[i],r[i] 表示第i个元素的范围。

$1 \le n \le 10^5$
# 分析
差分约束

设d[i]表示第i个位置的元素。

$l[i] \leq d[i] \leq r[i] = l[i] \leq d[i] - d[0] \leq r[i]$

-> $d[0] - d[i] \leq -l[i]$

-> $d[i] - d[0] \leq r[i]$

添加附加源 d[0] = 0

对于i < k,如果 $f[k] == f[i]$, 则有 $d[k] - d[i] \leq 0$

对于i < k,如果 $f[k] = f[i] + 1$, 则有$d[k] - d[i] \geq 1$ -> $d[i] - d[k] \leq -1$

然后跑最短路即可。
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
typedef long long ll;
typedef pair<int, int> pii;
typedef pair<ll,ll> pll;
const int INF = 0x7fffffff;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e5 + 5;
int f[maxn], l[maxn], r[maxn], loc[maxn];

vector<pii> G[maxn];
bool vis[maxn];
int inq[maxn]; // 记录入队次数
ll d[maxn];

inline void init(int n){
    for(int i=0;i<=n;i++) G[i].clear();
}
inline void addedge(int u,int v,int w){
    G[u].pb(mp(v,w));
    // G[v].pb(mp(u,w));
}
bool spfa(int s,int n){
    for(int i=0;i<=n;i++) {
        d[i] = INF;
        inq[i] = 0;
        vis[i] = 0;
    }
    d[s] = 0;
    queue<int> q;
    q.push(s);
    vis[s] = true;
    inq[s]++;
    while(!q.empty()){
        int u = q.front();
        q.pop();
        vis[u] = false;
        for(auto &V : G[u]){
            int v = V.first;
            ll w = V.second;
            if(d[v] > d[u] + w){
                d[v] = d[u] + w;
                if(!vis[v]){
                    vis[v] = true;
                    inq[v]++;
                    q.push(v);
                    if(inq[v] >= n) return false;
                }
            }
        }
    }
    return true;
}


int main() {
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    std::ios::sync_with_stdio(false);
    int T;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        clr(loc, -1);
        for (int i = 1; i <= n; i++) {
            cin >> f[i];
        }
        init(n);
        for (int i = 1; i <= n; i++) {
            cin >> l[i] >> r[i];
        }
        for (int i = 1; i <= n; i++) {
            addedge(0, i, r[i]);
            addedge(i, 0, -l[i]);
        }
        for (int i = 1; i <= n; i++) {
            if (loc[f[i] - 1] != -1) {
                int u = loc[f[i] - 1];
                addedge(i, u, -1);
            }
            if (loc[f[i]] != -1) {
                int u = loc[f[i]];
                addedge(u, i, 0);
            }
            loc[f[i]] = i;
        }
        spfa(0,n);
        for (int i = 1; i <= n; i++) {
            if (i == 1)
                cout << d[i];
            else
                cout << ' ' << d[i];
        }
        cout << '\n';
    }
}
```
