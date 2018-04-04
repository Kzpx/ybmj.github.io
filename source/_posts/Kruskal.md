---
title: Kruskal
comments: true
data: 2018-03-09 15:20:36
categories:
- ACM
- 图论
- 最小生成树
---

# kruskal

**思想**：
贪心+并查集

从权值最小的边开始遍历，若当前两个点在一个连通分量里，则忽略。

否则选取这条边加入到最小生成树中，当选取n-1条边后停止，因为只需要n-1条边即可连接n个点。

若图不连通，则会出现问题...

可以稍微模改一下 (●'◡'●)

## 模板
```cpp
void addedge(int u,int v,int w){
    edges.push_back(Edge(u,v,w));
}

int find(int x){ return par[x] == x ? x : find(par[x]);}

int kruskal(){
    int ans = 0;
    for(int i=0;i<n;i++) par[i] = i;
    sort(edges.begin(),edges.end());     //按权值排序
    int cnt = 0;
    for(int i=0;i<edges.size();i++){
        if(cnt >= n-1) break;
        Edge &now = edges[i];
        int x = find(now.u);
        int y = find(now.v);
        if(x != y){
            cnt++;
            par[x] = y;
            ans += now.w;
        }
    }
    return ans;
}
```
