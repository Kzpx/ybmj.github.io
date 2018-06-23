---
title: SPFA
comments: true
date: 2018-06-22 22:27:38
categories:
- ACM
- 图论
- 最短路
---

```cpp
vector<pii> G[maxn];
bool inq[maxn];
int cnt[maxn]; // 记录入队次数
int d[maxn];

inline void init(int n){
    for(int i=0;i<=n;i++) G[i].clear();
}
inline void addedge(int u,int v,int w){
    G[u].pb(mp(v,w));
    // G[v].pb(mp(u,w));
}
bool spfa(int s,int n){
    for(int i=0;i<=n;i++) {
        d[i] = INF;
        inq[i] = 0;
        cnt[i] = 0;
    }
    d[s] = 0;
    queue<int> q;
    q.push(s);
    inq[s] = true;
    cnt[s]++;
    while(!q.empty()){
        int u = q.front();
        q.pop();
        inq[u] = false;
        for(auto V : G[u]){
            int v = V.first;
            int w = V.second;
            if(d[v] > d[u] + w){    // 求最长路的话改成 <
                d[v] = d[u] + w;
                if(!inq[v]){
                    inq[v] = true;
                    cnt[v]++;
                    q.push(v);
                    if(cnt[v] >= n) return false;
                }
            }
        }
    }
    return true;
}
```