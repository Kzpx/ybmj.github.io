---
title: Floyd-Warshall
comments: true
date: 2018-03-09 15:18:39
categories:
- ACM
- 图论
- 最短路
---

# Floyd_Warshall
## 思想


通过不断的松弛操作求有向图或无向图上任意两点间的最短距离。

同时可以判断图中是否存在负环。（如果自身到自身的距离被更新，说明存在负环）

**转移：** 任意两点如i到k，其最短距离可以表示为dp[i][k]。

$$dp[i][k] = min(dp[i][k], dp[i][t] + dp[t][k])$$

表示i到k的最短距离可以由i到t的最短距离距离加上t到k的最短距离更新

通过枚举起点，中间节点和终点，可以得到任意两点间的最短距离。

**时间复杂度** ：$O(N^3)$

<!--more-->

## 传递闭包

**作用** : 判断任意两点间是否连通。

如果i和k之间有通路,则$dp[i][k] = 1$,否则为0.

那么递推式变为
$$dp[i][k] = dp[i][k]\  ||\  (dp[i][t]\  \&\&\  dp[t][k])$$

但因为复杂度较高，所以还是慎用。

## 模板
```cpp
const int maxn = 100;
int dp[maxn][maxn];
// dp初始化为正无穷
void addedge(int u, int v, int w) {
    dp[u][v] = w;
    // dp[v][u] = w;
}
bool floyd_warshall(int n) {
    for (int i = 0; i < n; i++) dp[i][i] = 0;
    for (int t = 0; t < n; t++) {
        for (int i = 0; i < n; i++) {
            for (int k = 0; k < n; k++) {
                if (dp[i][k] > dp[i][t] + dp[t][k]) {
                    dp[i][k] = dp[i][t] + dp[t][k];
                }
            }
        }
    }
    for (int i = 0; i < n; i++) {
        if (dp[i][i] < 0) {
            return false;  // exist negative circle
        }
    }
    return true;
}

```

## 练习题
