---
title: Trie
comments: true
date: 2018-04-13 09:53:53
categories:
- ACM
- 字符串
- Trie
---

# 介绍
**功能：**
主要用于保存字符集合。

**优点：**
可以在$O(len)$ ($len$为要查询单词长度) 时间内查询某个单词是否存在。

**缺点：**
需要大量的储存空间。典型的用空间换时间。

# 思想：
前缀树，顾名思义，通过记录每个单词的前缀来保存单词。

![](http://img1.ph.126.net/nAomEOJqoHgxXHvd2QPKoA==/6632471842050521443.png)

每个值为1的节点，代表这个单词存在。 上图的单词为： al,le,lk,f,fx.

<!--more-->
**数组开多大合适？**

如果有100个单词，每个单词长度不超过500，且全是小写字母。

那么我们假设每个单词单独是一条链，一共有100条链，每条链长度为500，所以$maxnode = 100\times500$

$types = 26$ 因为有26个字母。
# 代码：
```cpp
//数组版
struct Trie {
  int ch[maxnode][types];
  int val[maxnode];
  int sz;

  void init() {
    memset(ch[0], 0, sizeof(ch[0]));
    sz = 1;
  }

  inline int idx(char x) { return x - 'a'; }

  void insert(string line, int key) {
      int u = 0;
      for (int i = 0; i < line.size(); i++) {
          int v = idx(line[i]);
          if (ch[u][v] == 0) {
              memset(ch[sz], 0, sizeof(ch[sz]));
              val[sz] = 0;
              ch[u][v] = sz++;
          }
          u = ch[u][v];
      }
      val[u] = key;
    }

    void find();
};
```
```cpp
//左儿子，右兄弟版。 减少储存空间。
struct Trie {
    int head[maxnode];  // the i'th son.
    int next[maxnode];  // the i'th brother.
    char ch[maxnode];
    int tot[maxnode];
    int sz;

    void init() {
        ans = 0;
        tot[0] = head[0] = next[0] = 0;
        sz = 1;
    }

    void insert(string line) {
        int u = 0, v;
        tot[0]++;
        for (int i = 0; i < line.size(); i++) {
            bool found = false;
            for (v = head[u]; v != 0; v = next[v]) {
                if (ch[v] == line[i]) {
                    found = true;
                    break;
                }
            }
            if (!found) {
                v = sz++;
                tot[v] = head[v] = 0;
                ch[v] = line[i];
                next[v] = head[u];
                head[u] = v;
            }
            u = v;
            tot[u]++;
        }
    }

};
```

# 题目
## Remember the Word UVA - 1401 Trie + dp
### 题意
给出一个由n个不同单词组成的字典和一个长字符串。把这个字符串分解成若干个单词的连接（单词
可以重复使用），有多少种方法？
比如：有4个单词a,b,cd,ab,则abcd有两种分解方法: a+b+cd 和 ab+cd.

所有单词总共长度不超过300000。长字符串个数不超过4000. 每个长字符串不超过100。 全是小写字母。
### 分析
对于每个长字符串line,```dp[i]```表示字符串第i个位置到末尾共有几种组合方式。

转移方程则为$$dp[i] =\sum(dp[i + prefix(i...L)])$$

其中prefix(i..L) 表示从i到末尾这个字符串的每个前缀。若该前缀在字典树中存在则加上相应的dp的值。

比如： abcdef

设i为3，则prefix(3..5) 是 def,de,d ，为def的前缀。
### 代码
```cpp
// ybmj
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int mod = 20071027;
const int maxn = 300005;
const int sigma_size = 26;
const int maxnode = 4005 * 100;         //words number * words length
int dp[maxn];

struct Trie{
    int ch[maxnode][sigma_size];
    int val[maxnode];
    int sz;
    void init(){
        memset(ch[0],0,sizeof(ch[0]));
        sz = 1;
    }
    int idx(char c){
        return c - 'a';
    }

    void insert(string line,int v){
        int u = 0;
        for(int i=0;i<line.size();i++){
            int now = idx(line[i]);
            if(!ch[u][now]){
                memset(ch[sz],0,sizeof(ch[sz]));
                val[sz] = 0;
                ch[u][now] = sz++;
            }
            u = ch[u][now];
        }
        val[u] = v;
    }

    int query(const string &line){
        memset(dp,0,sizeof(dp));
        dp[line.size()] = 1;
        for(int i=line.size()-1;i >= 0;i --){
            int now = 0;
            for(int k=0;k+i < line.size();k++){
                int temp = idx(line[i+k]);
                if(!ch[now][temp]) break;
                now = ch[now][temp];
                if(val[now]){
                    dp[i] = (dp[i] + dp[i+k+1]) % mod;
                }
            }
        }
        return dp[0];
    }
};
Trie trie;
int main(){
    /*
    #ifndef ONLINE_JUDGE
    freopen("1.in","r",stdin);
    freopen("1.out","w",stdout);
    #endif
    */
    std::ios::sync_with_stdio(false);
    string line;
    int kase = 1;
    while(cin >> line){
        int n;
        trie.init();
        cin >> n;
        for(int i=0;i<n;i++){
            string temp;
            cin >> temp;
            trie.insert(temp,1);
        }
        int ans = trie.query(line);
        cout << "Case " << kase++ << ": " << ans << endl;
    }

}
```

## "strcmp()" Anyone? UVA - 11732  左兄弟右儿子版
### 题意
strcmp比较两个字符串的次数,比如比较than 和 that,需要比较7次才能判断是否相等，
比较there 和 the 也需要比较7次（因为结尾有'\0'）。

给定n个字符串，问需要比较总的次数是多少？

最多4000个字符串，每个串不超过1000个字符。
### 分析
将所有字符串都插入字典树中，对于每个插入的节点，其权值加一。

其余看代码吧。
### 代码
```cpp
// ybmj
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int INF = 0x3f3f3f3f;
const int NINF = 0xc0c0c0c0;
const int maxn = 4005;
const int maxnode = maxn * 1000;

struct Trie {
    int head[maxnode];  // the i'th son.
    int next[maxnode];  // the i'th brother.
    char ch[maxnode];
    ll tot[maxnode];
    int sz;
    ll ans;

    void init() {
        ans = 0;
        tot[0] = head[0] = next[0] = 0;
        sz = 1;
    }

    void insert(string line) {
        int u = 0, v;
        tot[0]++;
        for (int i = 0; i < line.size(); i++) {
            bool found = false;
            for (v = head[u]; v != 0; v = next[v]) {
                if (ch[v] == line[i]) {
                    found = true;
                    break;
                }
            }
            if (!found) {
                v = sz++;
                tot[v] = head[v] = 0;
                ch[v] = line[i];
                next[v] = head[u];
                head[u] = v;
            }
            u = v;
            tot[u]++;
        }
    }
    void debug() {
        for (int i = 0; i < 7; i++) cout << tot[i] << ' ';
        cout << endl;
    }
    void query(int u, int depth) {
        if (head[u] == 0) {
            ans += tot[u] * (tot[u] - 1) * depth;
        } else {
            int sum = 0;
            for (int v = head[u]; v != 0; v = next[v]) {
                sum += tot[v] * (tot[u] - tot[v]);
            }
            ans += sum / 2 * (2 * depth + 1);
            for (int v = head[u]; v != 0; v = next[v]) {
                query(v, depth + 1);
            }
        }
    }
};
Trie trie;

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
        trie.init();
        for (int i = 0; i < n; i++) {
            string line;
            cin >> line;
            line.push_back('!');        //代替'\0'
            trie.insert(line);
        }
        // trie.debug();
        trie.query(0, 0);
        cout << "Case " << kase++ << ": ";
        cout << trie.ans << endl;
    }
}
```
