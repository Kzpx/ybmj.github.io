---
title: Beautiful Land
comments: true
date: 2018-05-09 19:02:47
categories:
- ACM
- 杂题
---

# 题意
来源:[第十四届华中科技大学程序设计竞赛决赛F](https://www.nowcoder.com/acm/contest/119/F)

超大容量01背包

# 分析
dp[i][k] 表示前i个物品组成价值为k的最小容量

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
typedef long long ll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e4+5;
int c[105],v[105];
int dp[105][maxn]; // dp[i][k] 前i个物品组成价值为k的最小容量
 
int main(){
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    std::ios::sync_with_stdio(false);
    int T;
    cin >> T;
    while(T--){
        int n,C;
        cin >> n >> C;
        int sum = 0;
        for(int i=1;i<=n;i++){
            cin >> c[i] >> v[i];
            sum += v[i];
        }
        clr(dp[0],0x3f);
        dp[0][0] = 0;
        for(int i=1;i<=n;i++){
            for(int k=0;k<=sum;k++){
                if(k >= v[i]){
                    dp[i][k] = min(dp[i-1][k-v[i]] + c[i],dp[i-1][k]);
                }
                else dp[i][k] = dp[i-1][k];
            }
        }

        int ans = 0;
        for(int i=0;i<=sum;i++){
            if(dp[n][i] <= C){
                ans = i;
            }
        }
        cout << ans << endl;
    }
}
```
