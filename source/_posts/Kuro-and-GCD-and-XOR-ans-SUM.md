---
title: Kuro and GCD and XOR ans SUM
comments: true
date: 2018-05-18 12:39:24
categories:
- ACM
- 杂题
---

# 题意
来源:[cf979D](http://codeforces.com/contest/979/problem/D)

q次询问，每次询问有两种操作：

1. 向数组中插入一个u $(1 \leq u \leq 10^5)$
2. 给定x,k,s, 要从数组中找一个数v，使得其满足：$k \mid GCD(x, v)$, $x + v \leq s$, $x \oplus v$

$1 \leq x, k, s \leq 10^{5}$
# 分析
01字典树 +　暴力

首先考虑$x \oplus v$:

即在一个数组中找到一个数与给定的数的异或值最大，　这个经典的问题可以用01字典树解决。

再考虑$k \mid GCD(x, v)$:

可以推出 $k \mid x , k \mid v$, 我们可以对1~1e5中每个数建一棵字典树，然后将所有能被k整除的数插入到k的字典树中。 然后我们只要在这棵树上找与x异或最大的数即可。

最后考虑$x + v \leq s$:

对于字典树的每个节点，去维护一个最小的v值，如果要查询的x + val[root] > s 肯定是不行的

字典树的内存设置：

一共要插1e5个数,他们总共的因子个数不超过$n*log(n)$,每个数的二进制长度为20,所以
maxnode = 1e5 * log(1e5) * 20 

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

const int maxn = 1e5 + 5;
const int maxnode = 2e7 + 5;
const int types = 2;

bool vis[maxn];
struct Trie {
    int root[maxn];
    int ch[maxnode][types];
    int val[maxnode];
    int sz;

    void init() {
        for (int i = 0; i < maxn; i++) {
            root[i] = i;
            val[i] = INF;
            clr(ch[i], 0);
        }
        sz = maxn;
    }
    void insert(int x, int key) {
        int u = root[x];
        val[u] = min(val[u], key);
        for (int i = 18; i >= 0; i--) {
            int v = (key >> i) & 1;
            if (ch[u][v] == 0) {
                clr(ch[sz], 0);
                val[sz] = INF;
                ch[u][v] = sz++;
            }
            u = ch[u][v];
            val[u] = min(val[u], key);
        }
        // assert(sz < maxnode);
    }

    int query(int x, int key, int s) {
        int u = root[x];
        if (val[u] > s) return -1;
        int ret = 0;
        for (int i = 18; i >= 0; i--) {
            int v = (key >> i) & 1;
            if (ch[u][v ^ 1] != 0 && val[ch[u][v ^ 1]] <= s) {
                ret += ((v ^ 1) << i);
                u = ch[u][v ^ 1];
            } else {
                ret += (v << i);
                u = ch[u][v];
            }
        }
        return ret;
    }
};
Trie trie;

vector<int> f[maxn];

void init() {
    for (int i = 0; i < maxn; i++) f[i].clear();
    for(int i=1;i<maxn;i++){
        for(int k = i;k < maxn;k+=i){
            f[k].pb(i);
        }
    }
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("1.in", "r", stdin);
    freopen("1.out", "w", stdout);
#endif
    std::ios::sync_with_stdio(false);
    int n;
    while (cin >> n) {
        trie.init();
        clr(vis, 0);
        init();

        for (int i = 0; i < n; i++) {
            int op;
            cin >> op;
            if (op == 1) {
                int u;
                cin >> u;
                if (vis[u]) continue;
                vis[u] = true;
                for (auto i : f[u]) {
                    trie.insert(i, u);
                }
            } else {
                int x, k, s;
                cin >> x >> k >> s;
                if (x % k != 0) {
                    cout << -1 << endl;
                    continue;
                }
                cout << trie.query(k, x, s - x) << endl;
            }
        }
    }
}
```
