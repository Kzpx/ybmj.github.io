---
title: SG函数
comments: true
date: 2018-06-23 23:18:58
categories:
- ACM
- 数学
- 博弈
---

## 什么是SG函数

我们考虑Nim游戏，其实是有若干个子游戏组合而成的（每一堆石子看做一个子游戏

然后得到了结论：将每一堆石子的数量异或起来，若为零，则先手必败。

再考虑一般情况，某些博弈游戏也可以分解为若干个子游戏，那么Nim游戏的结论是否具有一般性呢？

那么每个子游戏的"石子数"就是该子游戏SG函数的值。

将每个子游戏的SG函数的值异或起来，如果为0，则先手必败。

## SG函数定义

SG(x) = mex{SG(y) | y 是 x的后继} 

其中mex表示第一个集合中没有的自然数

联系Nim博弈

SG(x) = mex{x-1, x-2 ... 0} = x

就是该堆的石子数


一般 SG(0) = 0 即为必败状态， SG(x) != 0, 即为必胜状态
## 模板

```cpp
const int maxn = 100;
int f[N];
int SG[maxn];  //
int S[maxn];   // x 的后继状态

void getSG(int n) {
    SG[0] = 0;  // clr(SG,0);
    for (int i = 1; i < maxn; i++) {
        clr(S, 0);
        for (int k = 0; f[k] <= i && k < n; k++) S[SG[i - f[k]]] = 1;
        for (int k = 0;; k++) {
            if (!S[k]) {
                SG[i] = k;
                break;
            }
        }
    }
}

// 单点SG查询
void init(){
    clr(SG,-1);
}
int getSG(int n, int foo) {
    if (SG[foo] != -1) return SG[foo];
    // int S[N] = {0};
    set<int> S;
    for (int k = 0; f[k] <= foo && k < n; k++) {
        int bar = getSG(n, foo - f[k]);
        S.insert(bar);
        // S[bar] = 1;
    }
    for (int k = 0;; k++) {
        if (S.find(k) == S.end()) {
            return SG[foo] = k;
        }
    }
}
```
