---
title: Sequence Swapping
comments: true
date: 2018-05-02 20:00:47
categories:
- ACM
- 杂题
---

# 题意
来源:[15届浙江省赛D](http://acm.zju.edu.cn/onlinejudge/showContestProblem.do?problemId=5755)

给你一个由左右括号组成的字符串，每个位置的括号都对应一个权值。

现在可以做一个操作： 交换相邻位置的括号，若第k个括号是'(',并且第k+1个括号是')',则获得对应权值相乘的分数。

该操作可做无限次，问得到的最大权值是多少？

字符串长度 $\leq 10^3$

$-10^3 \le$ 权值 $\le 10^3$
# 分析
首先需要注意到的是权值有正有负，这样就很难贪心了。

我们可以这么考虑，对于第i个左括号，如果它向右交换k个右括号,那么第i-1个左括号就可往右**多**交换k个。

根据这个思想就可以想到是个记忆化搜索的过程

$dp[i][k]:$ 表示第i个左括号向右至多交换k个右括号所得到的最大价值。

所以这里有一堆预处理等着你
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
#define pii pair<int,int> 
typedef long long ll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1e3+5;
int num[maxn];
char ch[maxn];
ll dp[maxn][maxn];
int pre[maxn][maxn];      // pre[i][k]: 第i个左括号向右交换k个右括号得到的价值
int x[maxn];        //x[i] 表示i位置开始的后缀和（右括号的数量
vector<pair<int,int> >le;  // le[i]: 第i个左括号的位置和他右边右括号的数量
vector<pair<int,int> > ri[maxn];   //ri[i]第i个左括号右边 每个右括号的位置和权值


ll dfs(int pos,int cnt){ // pos : i, cnt : k
    if(dp[pos][cnt] != -1) return dp[pos][cnt];
    ll ret = 0;
    int k;
    if(cnt == 0){   //不能交换和不交换有点区别
        if(pos == 0) return 0;
        k = x[le[pos].first];
        ret = dfs(pos-1,ri[pos-1].size() - k);
        if(ret<0)ret = 0;
        dp[pos][cnt] = ret;
        return ret;
    }

    for(int i=0;i<=cnt;i++){
        if(i == cnt) {
            k = x[ri[pos][cnt-1].first + 1]; 
        }
        else {
            k = x[ri[pos][i].first];
        }

        if(pos == 0){
            ret = max(ret,1LL * pre[pos][i] * num[le[pos].first]);
        }
        else ret = max(ret,dfs(pos - 1,ri[pos-1].size() - k) + 1LL*pre[pos][i] * num[le[pos].first]);
    }
    if(ret < 0) ret = 0;
    dp[pos][cnt] = ret;
    return ret;
}
int main(){
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    std::ios::sync_with_stdio(false);
    int T;
    scanf("%d",&T);
    while(T--){
        int n;
        scanf("%d",&n);
        scanf("%s",ch);
        for(int i=0;i<n;i++) {
            scanf("%d",&num[i]);
        }
        x[n] = 0;
        for(int i=n-1;i>=0;i--){
            x[i] = x[i+1] + (ch[i] == ')');
        }
        le.clear();
        for(int i=0;i<n;i++){
            if(ch[i] == '('){
                int cnt = 0;
                for(int k=i+1;k<n;k++){
                    if(ch[k] == ')') cnt++;
                }
                le.pb(mp(i,cnt));
                int pos = le.size() - 1;
                ri[pos].clear();

                for(int k=i+1;k<n;k++){
                    if(ch[k] == ')'){
                        ri[pos].pb(mp(k,num[k]));
                    } 
                }
                pre[pos][0] = 0;
                for(int k=1;k<=ri[pos].size();k++){
                    pre[pos][k] = pre[pos][k-1] + ri[pos][k-1].second;
                }
            }
        }
        for(int i=0;i<le.size();i++){
            for(int k=0;k<=ri[i].size();k++){
                dp[i][k] = -1;
            }
        }
        ll ans;
        if(le.size() == 0) ans = 0;
        else ans = dfs(le.size()-1,ri[le.size()-1].size());
        printf("%lld\n",ans);
    }

}
```