---
title: RMQ
comments: true
date: 2018-07-03 08:10:47
categories:
- ACM
- 数据结构
- RMQ
---

# RMQ

RMQ: Range Minimum Query 范围最小值

## 一维RMQ

$dp[i][k]$: 表示从i位置开始，长度为$2^k$的一段元素中的最小值。

$dp[i][k] = min(dp[i][k-1], dp[i + (1 << (k-1))][k-1])$

![](http://ozrmo3j0k.bkt.clouddn.com/Selection_010.png)

查询区间$[l,r]$的时候要先找到第一个k,使得$2^{k+1} > r - l + 1$

那么答案就是$min(dp[l][k],dp[r - (1 << k) + 1][k])$

时间复杂度为： 预处理nlogn, 查询logn

### 模板
```cpp
// nlog(n) 预处理， log(n) 查询
// 起始下标为1
const int maxn = 100;
int dp[maxn][maxn];
int a[maxn];
void rmq_init(int n) {
    for (int i = 0; i < n; i++) dp[i][0] = a[i];
    for (int k = 1; (1 << k) - 1 < n; k++) {
        for (int i = 0; i + (1 << k) - 1 < n; i++) {
            dp[i][k] = min(dp[i][k - 1], dp[i + (1 << (k - 1))][k - 1]);
        }
    }
}
int rmq(int l, int r) {
    int k = 0;
    while ((1 << (k + 1)) <= r - l + 1) k++;
    return min(d[l][k], d[r - (1 << k) + 1][k]);
}
```

## 二维RMQ

$dp[r][c][i][k]$: 表示$(r,c)$为左上角,$(r + 2^{i} - 1, c + 2^{k} - 1)$为右下角的矩阵中的最小值


```cpp
// 预处理 n*m*log(n)*log(m)， 查询 log(nm) 
// 起始下标为1
const int maxn = 100;
int dp[maxn][maxn][maxn][maxn];
int a[maxn][maxn];
void rmq_init(int n, int m) {
    for (int i = 1; i <= n; i++) {
        for (int k = 1; k <= m; k++) {
            dp[i][k][0][0] = a[i][k];
        }
    }
    for (int i = 0; (1 << i) <= n; i++) {
        for (int k = 0; (1 << k) <= m; k++) {
            if (!i && !k) continue;
            for (int r = 1; r + (1 << i) - 1 <= n; r++) {
                for (int c = 1; c + (1 << k) - 1 <= m; c++) {
                    if (!k)
                        dp[r][c][i][k] =
                            min(dp[r][c][i - 1][k],
                                dp[r + (1 << (i - 1))][c][i - 1][k]);
                    else
                        dp[r][c][i][k] =
                            min(dp[r][c][i][k - 1],
                                dp[r][c + (1 << (k - 1))][i][k - 1]);
                }
            }
        }
    }
}
int rmq(int x1, int y1,int x2,int y2) {
    int kx = 0 , ky = 0;
    while((1 << (kx+1)) <= x2 - x1 + 1) kx++;
    while((1 << (ky+1)) <= y2 - y1 + 1) ky++;
    int m1 = dp[x1][y1][kx][ky];
    int m2 = dp[x2 - (1 << kx) + 1][y1][kx][ky];
    int m3 = dp[x1][y2 - (1 << ky) + 1][kx][ky];
    int m4 = dp[x2 - (1 << kx) + 1][y2 - (1 << ky) + 1][kx][ky];
    return min(m1,min(m2,min(m3,m4)));
}
```

