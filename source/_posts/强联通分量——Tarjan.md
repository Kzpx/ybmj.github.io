---
title: 强联通分量——Tarjan
comments: true
date: 2018-03-09 15:13:59
categories:
- ACM
- 图论
- 强联通分量
---
# 强联通分量 Tarjan

## 概念
**强连通(Strongly Connected Compeonent)**

指在一张有向图中，各个点互相可达。则称此图强连通。

若图中有子图是强连通的，则该子图成为强连通分量。

实际问题中，强连通分量往往会被看成一个点来处理(缩点)。

学习的博客：[博客地址](http://blog.miskcoo.com/2016/07/tarjan-algorithm-strongly-connected-components)

<!--more-->
## Tarjan
本质是在 dfs的过程中去维护一些信息 ：

dfn数组： 时间戳，表示每个结点的访问顺序。 同时表示 一个强连通分量的“根”.
> 这里的意思是指：每走到一个新的元素，都会去初始化其 dfn[i] = low[i] = ++index, 表示以当前时间戳为根的一个强连通分量。
>
>为什么不用dfn[i] = low[i] = i 呢，因为搜索的顺序不一定是严格递增的。时间戳的作用
就是保持顺序搜索。
>
> 这里还有一个性质： 如果发现 dfn[i] != low[i], 说明以当前时间戳为根的强连通分量， 属于另一个更“早”的强连通分量。不理解的话可以先看模拟的过程
>
> 若 dfn[i] == low[i] , 说明又搜了一圈之后又搜回到当前结点，那么在这个搜索的过程中所有的元素（即栈内i节点之后进来的的所有元素） 都属于以当前时间戳为根的强连通分量。

low数组： 当前结点属于哪个“根” （ 即属于哪个强连通分量）

belong数组： 表示每个点属于哪一个强连通分量。

栈： 用来标记已经走过的点

### 模拟

**第一步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree1.png)

从第一个点开始搜索，最初的dfn[1] = low[1] = ++index(1)。 此时栈里的结点是1.

**第二步:**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree2.png)

接下来搜到第2个结点，同时也初始化dfn[2] = low[2] = ++index(2). 此时栈里的结点是 : 1 , 2.

**第三步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree3.png)

接下来搜到第3个结点，同时也初始化low[3] = low[3] = ++index(3) .此时栈里的结点是: 1, 2, 3.

然后发现没有结点可以走了，开始回溯，离开之前发现dfn[3] == low[3], 表示找了一个连通分量。

所以开始出栈，直到3结点为止，所有出栈的元素都是一个强连通分量。

所以3结点自己就是一个强连通分量。

此时栈里的结点是： 1， 2.

**第四步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree4.png)

从3结点离开后，回到2结点，然后搜到4结点。初始化dfn[4] = low[4] = ++index(4), 此时栈里的结点是: 1, 2 , 4.

接着继续搜，搜到1结点，但发现1结点已经被搜过了（在栈里），此时要做的就是更新 low[4] = dfn[1].

说明4结点属于 以 1结点的时间戳 为根的强连通分量。

此时栈里的元素是： 1, 2, 4.

**第五步:**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree5.png)

继续搜3结点，发现3结点以前搜过(dfn[3] != 0, 表示以前搜过)，所以不去管它。

**第六步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree6.png)

搜完3发现没有结点可搜了，所以要回溯，但离开4之前要判断 dfn[4] == low[4] ? 发现并不相等，说明这个强连通分量中还有其它点，所以不用管。

回到2之后，因为没结点可搜，继续回溯，离开2之前要判断 dfn[2] == low[2] ? 发现并不相等，所以不用管。

回到1之后，开始搜5结点， 更新 dfn[5] = low[5] = ++index(5)， 此时栈里元素是： 1, 2, 4, 5.

然后继续搜6结点，更新 dfn[6] = low[6] = ++index(6), 此时栈里元素是： 1, 2, 4, 5, 6.

**第七步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree7.png)

搜4结点，发现它搜过（在栈里），所以此时更新 low[6] = dfn[4]. 继续搜其它儿子。

**第八步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree8.png)

发现6结点没有其它儿子了，所以回溯到5结点，离开6之前判断 dfn[6] == low[6] ? 不等，不管。

回到5之后，要更新 low[5] = low[6].  **这里要注意！容易忘记！**

继续搜其它儿子。

**第九步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree9.png)

搜到7结点，更新 dfn[7] = low[7] = ++index(7), 此时栈里结点是： 1, 2, 4, 5, 6, 7.

然后继续搜8结点，更新 dfn[8] = low[8] = ++index(8), 此时栈里结点是: 1, 2, 4, 5, 6, 7, 8.

然后继续搜，发现5结点之前搜过（在栈里),所以更新 low[8] = dfn[5].

**第十步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree10.png)

然后继续搜9， 更新 dfn[9] = low[9] = ++index(9), 此时栈里结点是： 1, 2, 4, 5, 6, 7, 8, 9.

发现9没有儿子，所以要回溯，离开之前发现 dfn[9] == low[9], 所以开始出栈，直到9结点。

所有出栈元素构成一个强连通分量。

故9结点单独是个强连通分量。

**第十一步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree11.png)

回溯到8，此时 low[8] < dfn[8], 不用管，继续回退。

**第十二步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree12.png)

回溯到7，此时 low[7] < dfn[7], 不用管，继续回退。

**第十三步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree13.png)

回溯到5，此时 low[5] < dfn[5], 不用管，继续回退。

**第十四步：**

![](http://ozrmo3j0k.bkt.clouddn.com/search-tree14.png)

回溯到1， 此时 low[1] == dfn[1], 开始出栈， 直到1结点。

发现所有的元素都出栈了，即(1, 2, 4, 5, 6, 7, 8) 构成了一个强连通分量。

故原图中共有三个强连通分量。

**结束**

## 模板
```cpp
vector<int> G[maxn];        //存图
int scc;    //强连通分量的数量
int idx;  // 时间戳
int top;    // 栈顶
bool instack[maxn];
int dfn[maxn],low[maxn],belong[maxn],Stack[maxn];
// int num[maxn];      // 每个强连通分量的数量。 1 ~ scc
// int maps[maxn];      //缩点之后 每个点对应的新点的标号
void Tarjan(int u){
    dfn[u] = low[u] = ++idx;
    instack[u] = true;
    Stack[top++] = u;

    for(int i=0;i<G[u].size();i++){
        int v = G[u][i];
        if(!dfn[v]){
            Tarjan(v);
            low[u] = min(low[u],low[v]);
        }
        else if(instack[v])
            low[u] = min(low[u],dfn[v]);
    }
    if(dfn[u] == low[u]){
        ++scc;
        int cnt = 0;
        int now;
        while(top > 0){
            now = Stack[--top];
            instack[now] = false;
            belong[now] = u;
            ++cnt;
            if(now == u){
                // num[scc] = cnt;
                // maps[u] = scc;
                break;
            }
        }
    }
}

void solve(int n){
    memset(instack,0,sizeof(instack));
    memset(dfn,0,sizeof(dfn));
    scc = idx = top = 0;
    for(int i=1;i<=n;i++){      // 点的标号从1开始
        if(!dfn[i])
            Tarjan(i);
    }
}
```
