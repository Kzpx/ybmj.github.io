---
title: Perfect Groups
comments: true
date: 2018-05-09 23:03:51
categories:
- ACM
- 杂题
---

# 题意
来源:[cd980D](http://codeforces.com/contest/980/problem/D)

给定一个长度为n的数组，对于它的每个子段，最少需要分几组，使得每组中的元素，两两乘积都为完全平方数？

$1 \leq n \leq 5000$
# 分析
首先我们考虑如何判断两个数的乘积是否为完全平方数？ 很显然$O(\sqrt n)$是不行滴。

我们对每个数进行质因数分解，将其所有的平方因子全部去掉，这样只需要判断两数是否相等即可判断其乘积是否为完全平方数。

然后对于区间[i,k]中所有相等的数字可以分到一组，那么区间[i,k]的答案就是不同的数字的数量

暴力枚举所有子段即可。

需要注意的是0可以代替任何数。

而且若一个子段里全是0，那么其值应为1。
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
const int maxn = 1e4; 
int a[maxn],b[maxn],lastpos[maxn],cnt[maxn];
map<int,int> last;

inline void work(int idx){
    int x = a[idx];
    int flag = x > 0 ? 1 : -1;
    x *= flag;
    for(int i=2;i<=sqrt(x);i++){
        while(x && x % (i*i) == 0) x /= i*i;
    }
    a[idx] = x * flag;
}
int init(int n){
    last.clear();
    for(int i=1;i<=n;i++){
        work(i);
    }
    for(int i=1;i<=n;i++){
        b[i] = a[i];
    }
    sort(b+1,b+1+n);
    int sz = unique(b+1,b+1+n) - b - 1;
    bool flag = false;
    int ret = -1;
    for(int i=1;i<=n;i++){
        if(a[i] == 0) flag = true;
        a[i] = lower_bound(b+1,b+1+sz,a[i]) - b ;
        if(flag && ret == -1) ret = a[i];
        lastpos[i] = last[a[i]];
        last[a[i]] = i;
    }
    return ret;
}
int main(){
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    std::ios::sync_with_stdio(false);
    int n;
    while(cin >> n){
        for(int i=1;i<=n;i++){
            cin >> a[i];
        }
        int zero = init(n);
        clr(cnt,0);
        for(int i=1;i<=n;i++){
            int val = 0;
            for(int k=i;k<=n;k++){
                if(lastpos[k] < i && a[k] != zero){
                    val = val + 1;
                }
                cnt[val]++;
            }
        }
        cnt[1] += cnt[0];
        for(int i=1;i<=n;i++) cout << cnt[i] << ' ';
        cout << endl;
    }
}
```
