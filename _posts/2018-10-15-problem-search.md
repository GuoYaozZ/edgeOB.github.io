---
layout:     post
title:      搜索题补题集
subtitle:   "关于搜索的一些小注意事项"
date:       2018-10-15 21:30:00
author:     "programfiles"
header-img: "img/in-post/problems.jpg"
tags:
    - 搜索
---
# Counting Cliques 
* [hdu5952-2016shenyangICPC-E](http://acm.hdu.edu.cn/showproblem.php?pid=1430)<br>
给出一个无向图，求所有子图中的完全图的个数<br>
从数据量来看可以暴力搜索，事实上这道题从数据来看并不需要过多的剪枝就可以通过，但是题目中不乏需要注意的点》》题目虽然给的是无向图，但是在存储的时候需要按照有向图来存储，因为比如s=3, 点集134,143,314,341....这些均可以归属到一种，所以只需要存储u < v的边<br>
这样爆搜确实可以，不过从点集来说这道题其实，可以看成是集合问题，举个栗子：1连接的顶点是345，那么如果遍历到3连接的是145，在回头查找时就可以只查找45，1可以不再使用，这样一步一步缩小所需集合，然后加上一些剪纸如剩余点数不足（s=5然而3之后只有45两个点最大只能是点集为4）可以直接结束遍历

```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn = 105;
bool g[maxn][maxn];
int m, n, s;
void init() //初始化图 
{
    int u, v;
    memset(g, 0, sizeof g);
    scanf("%d%d%d", &n, &m, &s);
    for (int i = 0; i < m; ++i)
    {
        scanf("%d%d", &u, &v);
        g[u][v] = g[v][u] = true;
    }
}
void dfs(int t, int &cnt, int d, int *ns, int l)
{
    if (d == s)//d计数器，s元数
    {
        ++cnt;
        return;
    }
    int nws[maxn];
    int nwl = 0;
    for (int i = 0; i < l; ++i)
    {
        if (!g[t][ns[i]]) continue; //t当前点 
        nws[nwl++] = ns[i]; //nws当前点相邻的下一批点 
    }
    for (int i = 0; i < nwl; ++i) //下一批点中的这个点已不合条件//即使能继续走最后也不够了 
    {
        if (nwl - i < s - d) return;
        dfs(nws[i], cnt, d + 1, nws + (i + 1), nwl - (i + 1)); //只需要考虑i+1以后的，l同理缩短因为需要点数减少 
    }
}
int solve()
{
    init();
    int ns[maxn];
    for (int i = 0; i <= n; ++i)
    {
        ns[i] = i;
    } //初始化为全集 
    int ans = 0;
    for (int i = 1; i <= n - s + 1; ++i)
    {
        int cnt = 0;
        dfs(i, cnt, 1, ns + i + 1, n - i); //起始点，计数器，剩余点数，交集/全 
        ans += cnt;
    }
    return ans;
}
int main()
{
    int t;
    scanf("%d", &t);
    while (t--)
        printf("%d\n", solve());
    return 0;
}
```
# codeforces Round 516 Div.2 D
这个题给你一个迷宫点阵，让你判断能走到哪些点，不过限制条件就是从A到B点向左走的次数不能超过x步，向右不能超过y步。
我以为是简单bfs直接走的送分题，后来一直过不去才发现原来有坑在，可以参考一下下面这一组hack->：
```cpp
20 7
3 6
5 2
......*
.****.*
.****.*
....*.*
**.**.*
**.**.*
**.**.*
**.**.*
**.**.*
**.**.*
**.**.*
**.**.*
**.**.*
**.**.*
**.**.*
**.**.*
**.**.*
**.**.*
**....*
*******
------------向左走计数
2222220
2000020
2000020     2000020
2221020     2220020
0020020     0020020
0020020
0020020
0020020
0020020
0020020
0020020
0020020
0020020
0020020
0020020
0020020
0020020
0020020
0022220
0000000
------------向右走计数
1111110
1000010
1000010
1111010
0010010
0010010
0010010
0010010
0010010
0010010
0010010
0010010
0010010
0010010
0010010
0010010
0010010
0010010
0011110
0000000
```
观察输出数据的单列三行不同点就是有一个位置在右侧错误的情况下是走不到的，这是因为从左侧路径遍历下来走不到那个点但是从右侧遍历却可以走到，解决的方法之一就是开一个标记数组标记左右还可以继续走的步数，取最大值，这样就可以让走不到的点的向右可走
```cpp
int bfs(int x1, int y1)
{
    node fir, nex;
    fir.x = x1, fir.y = y1, fir.left = x, fir.right = y;
    forl[x1][y1] = 0;
    forr[x1][y1] = 0;
    vis[fir.x][fir.y] = 1;
    queue<node> Q;
    Q.push(fir);
    while(!Q.empty())
    {
        fir = Q.front();
        Q.pop();
        for(int i = 0; i < 4; i++)
        {
            nex.x = fir.x + tp[i][0];
            nex.y = fir.y + tp[i][1];
            nex.left = fir.left;
            nex.right = fir.right;

            if(nex.x < 0 || nex.x > n-1) continue;
            if(nex.y < 0 || nex.y > m-1) continue;
            if(maze[nex.x][nex.y] == '*')continue;
            if(nex.left < 0)             continue;
            if(nex.right < 0)            continue;

            if(tp[i][1] == 1)        nex.right --;
            if(tp[i][1] == -1)        nex.left --;
            if(nex.left < 0)             continue;
            if(nex.right < 0)            continue;

            if(!vis[nex.x][nex.y] || forl[nex.x][nex.y] < nex.left || forr[nex.x][nex.y] < nex.right)
            {
                forl[nex.x][nex.y] = max( forl[nex.x][nex.y], nex.left );
                forr[nex.x][nex.y] = max( forr[nex.x][nex.y], nex.right);
                vis[nex.x][nex.y] = 1;
                Q.push(nex);
            }
        }
    }
}
```
