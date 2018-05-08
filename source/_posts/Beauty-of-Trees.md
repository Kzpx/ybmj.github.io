---
title: Beauty of Trees
comments: true
date: 2018-05-08 20:31:16
categories:
- ACM
- 杂题
---

# 题意
来源:[第十四届华中科技大学程序设计竞赛决赛A](https://www.nowcoder.com/acm/contest/119/A)

给你m个询问，每次询问包括一个区间，以及这个区间的异或和。

现在问你哪几个询问是错的。 

$1≤M≤10^5$

$10^5≤l_i ≤r_i≤10^5$
# 分析
逻辑判断

带权并查集

对于每个l,r ：

1. 若l和r不在一个集合中，则合并两个集合。
2. 若l和r在同一个集合中，则先将l和r进行路径压缩，然后进行权值验证。
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
const int maxn = 1e5+5;
int par[maxn],val[maxn];
 
int find(int x){
    if(par[x] == x) return x;
    int rt = find(par[x]);              // !
    val[x] = val[x] ^ val[par[x]];
    return par[x] = rt;
}
void merge(int l,int r,int w){
    int pl = find(l);
    int pr = find(r);
    if(pl != pr){
        val[pr] = val[l] ^ w ^ val[r];  // !
        par[pr] = pl;
    }
}
bool same(int l,int r){
    return find(l) == find(r);
}
int main(){
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    std::ios::sync_with_stdio(false);
    int n,m;
    while(cin >> n >> m){
        for(int i=0;i<=n;i++) par[i] = i;
        clr(val,0);
        bool flag = true;
        for(int i=0;i<m;i++){
            int l,r,w;
            cin >> l >> r >> w;
            l--;
            if(!same(l,r)) merge(l,r,w);
            else if((val[r] ^ val[l]) != w){
                flag = false;
                cout << i + 1 << endl;
            }
        }
        if(flag) cout << -1 << endl;
    }
 
}
```
