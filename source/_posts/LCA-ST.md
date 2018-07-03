---
title: LCA-ST
comments: true
date: 2018-07-03 19:57:42
categories:
- ACM
- 图论
- 最近公共祖先LCA
---

# ST

这是一种在线计算LCA的算法，时间复杂度为： 预处理nlogn, 查询logn

## 思想

将树型转化为区间形式，然后再RMQ所查区间内深度最小的点，即为lca

这种遍历方式需要两倍的存储空间

![](http://ozrmo3j0k.bkt.clouddn.com/st1.jpg)

![](http://ozrmo3j0k.bkt.clouddn.com/st2.jpg)

## 模板
```cpp
const int maxn = 100;
vector<int> G[maxn];
int deep[maxn * 2];    // deep[u]: u的深度
int trav[maxn * 2];    // 遍历之后的数组
int fir[maxn];     // fir[u]: u点在trav中出现的第一个位置的下标
int dp[maxn][maxn]; // RMQ 记录的是最小深度的下标
int tot;
void init() {
    tot = 0;
    clr(fir, 0);
}

void dfs(int u, int fa, int h) {
    trav[++tot] = u;
    fir[u] = tot;
    deep[tot] = h;
    for (auto &v : G[u]) {
        if (v != fa && !fir[v]) {
            dfs(v, u, h + 1);
            trav[++tot] = u;
            deep[tot] = h;
        }
    }
}

void ST(int n) {
    for (int i = 1; i <= n; i++) dp[i][0] = i;
    for (int k = 1; (1 << k) <= n; k++) {
        for (int i = 1; i + (1 << k) - 1 <= n; i++) {
            int foo = dp[i][k-1];
            int bar = dp[i + (1 << (k-1))][k-1];
            if(deep[foo] < deep[bar]) dp[i][k] = foo;
            else dp[i][k] = bar;
        }
    }
}

int RMQ(int l,int r){
    int k = 0;
    while( (1 << (k+1)) <= r - l + 1) k++;
    int pos = min(dp[l][k], dp[r - (1 << k) + 1][k]);
    return trav[pos];
}

int LCA(int u,int v){
    u = fir[u];
    v = fir[v];
    if(u > v) swap(u,v);
    return RMQ(u,v);
}
```
