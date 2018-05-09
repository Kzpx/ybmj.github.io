---
title: Cards Game
comments: true
date: 2018-05-09 19:06:34
categories:
- ACM
- 杂题
---

# 题意

有n堆牌，每一堆牌有m张，alice只可以每一堆牌的最上面的一张，bob只可以取每一堆牌最下面的一张，每张牌都有一个权值。 两人都采取最优策略，问最后alice和bob各取了多少?

$1≤n,m≤100$
# 分析
这有个性质，对于偶数张的牌堆，alice和bob一定是各取一半，对于奇数张的牌堆，最中间那张从大到小排序后alice和bob按顺序拿
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
const int maxn = 105;
int a[maxn];
vector<int> mid;
int main(){
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);
    int n;
    while(cin >> n){
        mid.clear();
        int alice = 0, bob = 0;
        for(int i=0;i<n;i++){
            int m;
            cin >> m;
            for(int k=0;k<m;k++){
                cin >> a[k];
                if(k<m/2)alice += a[k];
                else bob += a[k];
            }
            if(m & 1){
                bob -= a[m/2];
                mid.pb(a[m/2]);
            }
        }
        sort(mid.begin(),mid.end(),greater<int>());
        for(int i=0;i<mid.size();i++){
            if(i & 1) bob += mid[i];
            else alice += mid[i];
        }
        cout << alice << ' ' << bob << endl;
    }
}
```
