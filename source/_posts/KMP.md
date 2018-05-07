---
title: KMP
comments: true
date: 2018-04-13 09:51:56
categories:
- ACM
- 字符串
- KMP
---
 
# 介绍
**功能：**
用来查找指定字符串。（从A串中查找B串）

**优点：**
O(n)的复杂度，比较快。

**本质：**
序列检测器。
<!--more-->
# 思想
![](http://img0.ph.126.net/Lq09RFOcBsW5E-vkWGhTEg==/6632310213841238574.png)

如图，进行第一次匹配的时候，在红色地方失配，接下来第二次直接从位置二开始匹配，这就是kmp算法的精髓。


去寻找最长的公共前后缀，一旦失配，直接“后缀变前缀”，从上图来说ababe,最长
公共前后缀就是ab,（不考虑e，因为假设在e处失配）。

![](http://ozrmo3j0k.bkt.clouddn.com/kmp.PNG)

如图，在3处失配后，直接跳到1位置继续进行匹配。

这个back数组的含义就是：在第i位失配之后，[0, back[i]-1] 已经是成功的匹配了。 只要继续匹配第i位和back[i]即可。

# 模板
```cpp
struct MP{
    int back[maxn];

    void getfail(string line){
        int i,k;
        k = back[0] = -1;
        i = 0;
        while(i < line.size()){
            while(k != -1 && line[i] != line[k]) k = back[k];
            back[++i] = ++k;
        }
    }
    int match(string T,string P){
        int i = 0,k = 0;
        int ret = 0;
        getfail(P);
        while(i < T.size()){
            while(k != -1 && T[i] != P[k]) k = back[k];
            ++i;++k;
            if(k >= P.size()){
                ret++;
                k = back[k];
            }
        }
        return ret;
    }
};
```
# 题目
## Period UVA - 1371 back数组的应用
### 题意
给定一个长度为$n(2\leq n \leq 10^6)$的字符串S，求它每个前缀的最短循环节，换句话说，对于每个$i(2\leq i\leq n)$
,求个一个最大的整数$K(K \geq 1)$（如果K存在），使得S的前i个字符组成的前缀是某个字符串重复K
次得到的。输出所有存在K的i和对应的K。

比如对于字符串aabaabaabaab,只有当i = 2,6,9,12时K存在,且分别为2，2，3，4.
### 分析
回想一下mp中getfail函数的含义。求第i个字符前的最长公共前后缀。

|a|b|c|a|b|c|a|b|c| | | |
|-|-|-|-|-|-|-|-|-|-|-|-|
| | | |a|b|c|a|b|c|a|b|c|

如图，back[8] = 5,说明最后一位前面最长公共前后缀的长度为5，算上最后一位，那么最长
公共前后缀的长度就为6.

试想，最长的前后缀有六位相同，也就是说中间有重叠的三个。

![](http://img0.ph.126.net/fZVyLNhs1mYVRhdTVqtP-g==/6632510324957491680.png)

如图， 1 = a, 2 = b, 3 = c.

2 = a , 3 = b.

所以1 = 2 = 3 = a = b = c.

|a|b|c|e|e|a|b|c| | | | | |
|-|-|-|-|-|-|-|-|-|-|-|-|-|
| | | | | |a|b|c|e|e|a|b|c|

![](http://img2.ph.126.net/vKlQAcbEW_APh2ltDqIz2g==/6632657659513018772.png)

如图， 1 = a, 2 = b, 3 = c.

3 = a.

所以没有循环节。

### 代码
```cpp
// ybmj
#include <iostream>
#include <string>
using namespace std;
typedef long long ll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 1000000 + 5;

struct MP {
    int back[maxn];
    void getFail(string P) {
        back[0] = back[1] = 0;
        for (int i = 1; i < P.size(); i++) {
            int k = back[i];
            while (k && P[i] != P[k]) k = back[k];
            back[i + 1] = P[i] == P[k] ? k + 1 : 0;
        }

        // for (int i = 0; i < P.size(); i++) {
        // cout << P[i] << ' ' << back[i] << endl;
        // }
    }
};
MP mp;
int main() {
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    std::ios::sync_with_stdio(false);

    int n;
    int kase = 1;
    while (cin >> n) {
        if (n == 0) break;
        string line;
        cin >> line;
        // cout << line << endl;
        mp.getFail(line);
        cout << "Test case #" << kase++ << '\n';
        for (int i = 2; i <= line.size(); i++) {
            if (mp.back[i] > 0 && i % (i - mp.back[i]) == 0) {
                cout << i << ' ' << i / (i - mp.back[i]) << '\n';
            }
        }
        cout << '\n';
    }
}
```

## Obsessive String CodeForces - 494B    mp + dp
### 题意
给定一个字符串T，让你选两个集合$A(a_i)，B(b_i)$，对于其中的$s_{a_i} ··· s_{b_i}$,含有
指定字符串P。问总共有多少种不同的集合？ 结果模1e9+7. $(1 \leq T,P \leq 10^5)$



### 分析
我们只要保证所选的$s_{a_i} ··· s_{b_i}$中至少包含一个P串即可。

考虑dp。

dp[i] 表示以第i个字符结尾的前i个字符的选择方式。

关键是如何转移？

假设P串的长度为m。

当T[i-m+1 ... i] 与P串不同时,dp[i] = dp[i-1].

否则，我们可以将 T[k...i] (k < i-m+1) 作为集合中的一个确定的元素$(a_z = k,b_x = i)$，
然后我们可以从T[1...k]中选择子串放到这个集合中，这样的选择一共有$\sum_{x=1}^{i-m} (sum[x]+1)$.
多加1是因为，这个子串我可以什么都不选。

sum[i] 表示第i个字符前一共有多少种选择方式。

那么如何判断T[i-m+1 .. i] 与P串是否相同呢？

当然是用mp算法，若相同，则将flag[i]记为1，否则为0.

### 代码
```cpp
#include<iostream>
#include<cstdio>
#include<string.h>
#include<math.h>
#include<string>
#include<map>
#include<set>
#include<vector>
#include<algorithm>
#include<queue>
#include<stack>
#include<iomanip>
using namespace std;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
typedef long long ll;
const int mod = 1e9+7;
const int maxn = 100005;
bool flag[maxn];
int dp[maxn],sum[maxn],tot[maxn];
struct MP{
    int back[maxn];
    void getfail(string P){
        back[0] = back[1] = 0;
        for(int i=1;i<P.size();i++){
            int k = back[i];
            while(k && P[i] != P[k]) k = back[k];
            back[i+1] = P[i] == P[k] ? k+1 : 0;
        }
    }
    void match(string T,string P){
        getfail(P);
        int k = 0;
        for(int i=0;i<T.size();i++){
            while(k && T[i] != P[k]) k = back[k];
            if(T[i] == P[k]) k++;
            if(k == P.size()){
                flag[i] = 1;
                k = back[k];
            }
        }
    }
};
MP mp;
int main(){
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    std::ios::sync_with_stdio(false);
    string T,P;
    while(cin >> T >> P){
        memset(flag,0,sizeof(flag));
        mp.match(T,P);
        dp[0] = 0;
        sum[0] = 0;
        tot[0] = 0;
        for(int i=1;i<=T.size();i++){
            if(flag[i-1]){
                dp[i] = (tot[i-P.size()] +i-P.size()+1) % mod;
                sum[i] = (sum[i-1] + dp[i]) % mod;
                tot[i] = (tot[i-1] + sum[i]) % mod;
            }
            else{
                dp[i] = dp[i-1];
                sum[i] = (sum[i-1] + dp[i]) % mod;
                tot[i] = (tot[i-1] + sum[i]) % mod;
            }
        }
        cout << sum[T.size()] << '\n';
    }
}

```
