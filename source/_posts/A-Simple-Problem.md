---
title: A Simple Problem
comments: true
date: 2018-05-09 17:35:50
categories:
- ACM
- 杂题
---

# 题意
来源:[第十四届华中科技大学程序设计竞赛决赛E](https://www.nowcoder.com/acm/contest/119/E)

给你两个数组，问你从第一个数组中存在多少个子段与第二个数组同构？

同构的意思是两个数组的结构相同： aabb 和 ccdd同构
$1 \leq n \leq 2 \times 10^5$

$1 \leq m \leq 10^5$
# 分析
KMP

根据这个同构的特点，我们只需要记录上一个和当前数字相同的距离即可。如果没有则记为-1.

如：
```
1 2 0 2 0 1 -> -1 -1 -1 2 2 5
1 2 1 2 -> -1 -1 2 2
```

然后跑kmp

注意这里的kmp要魔改一下下

如果距离大于m的要当做-1看待

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
typedef pair<ll, ll> pll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 2e5 + 5;
int s[maxn], p[maxn];
int last[15];
struct MP{
    int back[maxn];

    void getfail(int m){
        int i,k;
        k = back[0] = -1;
        i = 0;
        while(i < m){
            while(k != -1 && ((p[i] > k) ? (p[k] != -1) : (p[i] != p[k]))) k = back[k];
            back[++i] = ++k;
        }
    }
    int match(int n,int m){
        int i = 0,k = 0;
        int ret = 0;
        getfail(m);
        while(i < n){
            while(k != -1 && ((s[i] > k) ? (p[k] != -1) : (s[i] != p[k]))) k = back[k];
            ++i;++k;
            if(k >= m){
                ret++;
                k = back[k];
            }
        }
        return ret;
    }
};
MP kmp;

void init(int n, int m) {
    clr(last, -1);
    for (int i = 0; i < n; i++) {
        int temp = s[i];
        if (last[s[i]] == -1)
            s[i] = -1;
        else
            s[i] = i - last[s[i]];
        last[temp] = i;
    }
    clr(last, -1);
    for (int i = 0; i < m; i++) {
        int temp = p[i];
        if (last[p[i]] == -1)
            p[i] = -1;
        else
            p[i] = i - last[p[i]] ;
        last[temp] = i;
    }
}

void debug(int n, int m) {
    for (int i = 0; i < n; i++) cout << s[i] << ' ';
    cout << endl;
    for (int i = 0; i < m; i++) cout << p[i] << ' ';
    cout << endl;
}

int main() {
    /*j
#ifndef   
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
*/
    std::ios::sync_with_stdio(false);
    int n, k;
    while (cin >> n >> k) {
        for (int i = 0; i < n; i++) cin >> s[i];
        int m;
        cin >> m;
        for (int i = 0; i < m; i++) cin >> p[i];
        init(n, m);
        // debug(n, m);
        int ans = kmp.match(n, m);
        cout << ans << endl;
    }
}
```
