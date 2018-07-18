---
title: HLD
comments: true
date: 2018-07-03 22:44:32
categories:
- ACM
- 数据结构
- 树链剖分
---

感谢 [大神博客](https://www.cnblogs.com/ivanovcraft/p/9019090.html)

# HLD

HLD: Heavy-Light Decomposition 轻重链剖分

将一棵树转化为若干链拼接而成的区间.然后在这个区间上就可以使用一些数据结构来操作.

复杂度: 因为是轻重链剖分,所以跳跃的复杂度不会超过$log(n)$次

其实树链剖分并不是一个复杂的算法或者数据结构，只是能把一棵树拆成链来处理而已，换一种说法，树链剖分只是xxx数据结构/算法在树上的推广，或者说，树链剖分只是把树hash到了几段连续的区间上。

因为这东西本身没有功能，具体功能都是靠具体数据结构实现的，所以复杂度就是$O(logn * ??)$。如果套上了线段树，就是$O(log^2 n)$. 如果套上了树套树，那就是$O(log^3 n)$.如果你用树链剖分和树套树写区间第k大，那就是$O(log^4 n)$.

——出自[大神博客](https://oi.abcdabcd987.com/summary-of-heavy-light-decomposition/)

## 讲解

重儿子：父亲节点的所有儿子中子树结点数目最多的结点；

轻儿子：父亲节点中除了重儿子以外的儿子；

重边：父亲结点和重儿子连成的边；

轻边：父亲节点和轻儿子连成的边；

重链：由多条重边连接而成的路径；

轻链：由多条轻边连接而成的路径；

par[u]	保存结点u的父亲节点

dep[u]	保存结点u的深度值

sz[u]	保存以u为根的子树节点个数

son[u]	保存重儿子

rk[u]	保存当前dfs标号在树中所对应的节点

top[u]	保存当前节点所在链的顶端节点

id[u]	保存树中每个节点剖分以后的新编号（DFS的执行顺序）


![](http://ozrmo3j0k.bkt.clouddn.com/hld1.png)

上图中，红点为top点， 粗边为重边。

![](http://ozrmo3j0k.bkt.clouddn.com/hld2.png)


**注意**

数据结构维护的是树链剖分后的序列（id序列）

当题目中给的是边权时，要将边权下放变成点权（下放给深度大的点）

查询的时候要注意最后公共的lca是不算进去的，因为的权值是它与它父亲的边权。

## 模板
```cpp
const int maxn = 100;

struct HLD {
    int n, dfn;
    int sz[maxn], top[maxn], son[maxn], dep[maxn], par[maxn], id[maxn],rk[maxn];
    vector<int> G[maxn];
    void init(int n) {
        this->n = n;
        clr(son, -1);
        dfn = 0;
        for (int i = 0; i <= n; i++) G[i].clear();
    }
    void addedge(int u,int v){
        G[u].pb(v);
        G[v].pb(u);
    }
    void dfs(int u,int fa,int d){
        dep[u] = d;
        par[u] = fa;
        sz[u] = 1;
        for(auto &v:G[u]){
            if(v == fa) continue;
            dfs(v,u,d+1);
            sz[u] += sz[v];
            if(son[u] == -1 || sz[v] > sz[son[u]]) son[u] = v;
        }
    }
    void link(int u,int t){
        top[u] = t;
        id[u] = ++dfn;
        rk[dfn] = u;
        if(son[u] == -1) return ;
        link(son[u],t);
        for(auto &v:G[u]){
            if(v != son[u] && v != par[u]) 
                link(v,v);
        }
    }

    void update(int u,int v,int w) {} // 数据结构
    int query(int u,int v) {}         // 数据结构

    void update_path(int u,int v,int w){
        while(top[u] != top[v]){
            if(dep[top[u]] < dep[top[v]]) swap(u,v);
            update(id[top[u]],id[u],w);
            u = par[top[u]];
        }
        // if(u == v) return;   // 边权变点权
        if(dep[u] > dep[v]) swap(u,v);
        update(id[u],id[v],w);
        // update(id[u] + 1,id[v],w);  // 边权变点权

    }
    int query_path(int u,int v){
        int ret = 0;
        while(top[u] != top[v]){
            if(dep[top[u]] < dep[top[v]]) swap(u,v);
            ret += query(id[top[u]],id[u]);
            u = par[top[u]];
        }
        // if(u == v) return ret;   // 边权变点权
        if(dep[u] > dep[v]) swap(u,v);
        ret += query(id[u],id[v]);
        // ret += query(id[u] + 1,id[v]);  // 边权变点权
        return ret;
    }

};
```