---
layout:     post
title:      "ACM-Data Structure"
subtitle:   "数据结构"
date:       2019-06-01 09:00:00
author:     "programfiles"
header-img: "img/in-post/ACM.jpg"
tags:
    - ACM
---

* [线段树的单点更新与区间查询](#jump1)<br>
* [线段树的区间更新与区间查询](#jump2)<br>
* [线段树的区间合并](#jump3)<br>
* [扫描线](#jump4)<br>
* [可持久化线段树](#jump5)<br>
* [简单并查集的查找合并以及删除](#jump6)<br>
* [树状数组](#jump7)<br>
* [单调栈与单调队列](#jump8)<br>

<span id="jump1"></span> 
# 线段树的单点更新与区间查询

博客中的线段树的风格来自[胡浩大神（转载）](https://www.cnblogs.com/Mu-Tou/archive/2011/08/11/2134427.html)：<br>
maxn是题目给的最大区间,而节点数要开4倍,确切的来说节点数要开大于maxn的最小2x的两倍<br>
lson和rson分辨表示结点的左儿子和右儿子,由于每次传参数的时候都固定是这几个变量,所以可以用预定于比较方便的表示<br>
以前的写法是另外开两个个数组记录每个结点所表示的区间,其实这个区间不必保存,一边算一边传下去就行,只需要写函数的时候多两个参数,结合lson和rson的预定义可以很方便<br>
PushUP(int rt)是把当前结点的信息更新到父结点<br>
PushDown(int rt)是把当前结点的信息更新给儿子结点<br>
rt表示当前子树的根(root),也就是当前所在的结点<br><br>

###### [HDU1166](http://acm.hdu.edu.cn/showproblem.php?pid=1166)
这个估计是全世界公认的线段树入门题了，，最简单明了的单点更新与区间查询，这里先放上最基础的函数，几乎单点修改与区间查询的问题都是参照这个代码的函数进行了一些不同程度的修改产生的。<br>下面是代码和[用法](https://github.com/edgeOB/source-code/blob/master/HDU1166.cc)
```cpp
#define lson l, m, rt<<1
#define rson m+1, r, rt<<1|1
const int maxn = 55555;
int tree[maxn<<2];
void push_up(int rt) {
    tree[rt] = tree[rt<<1] + tree[rt<<1|1];
}
void build(int l, int r, int rt) {
    if(l == r) {
        scanf("%d", &tree[rt]);
        return;
    }
    int m = (l+r)>>1;
    build(lson);
    build(rson);
    push_up(rt);
}
void update(int p, int add, int l, int r, int rt) {
    if(l == r) {
        tree[rt] += add;
        return;
    }
    int m = (l+r)>>1;
    if(p <= m) update(p, add, lson);
    else update(p, add, rson);
    push_up(rt);
}
int query(int L, int R, int l, int r, int rt) {
    if(L <= l && R >= r) {
        return tree[rt];
    }
    int m = (l+r)>>1;
    int ret = 0;
    if(L <= m) ret += query(L, R, lson);
    if(R > m) ret += query(L, R, rson);
    return ret;
}
```

###### [HDU1394](http://acm.hdu.edu.cn/showproblem.php?pid=1394)
这个题也是单点修改和区间查询，第一次做的时候没有想出来，鶸太垃圾了，后来看了题解才反应过来，就是一个简单的逆序对统计问题，用树状数组其实更好做，毕竟代码少<br>
每次新输入数列的一个数a[i]的时候都查找这个数后边（比他大的部分）目前已经插入了多少个数，因为这些数比他大并且在他之前插入的，查找完之后把a[i]update上去，最后再来一遍ON的遍历，每一次将第一个数移到最后的时候，第一个数原本对逆序数的影响就是a[i]（a[i] = 2， 则有0和1）个，因为后边比他小的，移到最后又多出来n-1-a[i]个<br>
具体细节见[HDU1394](https://github.com/edgeOB/source-code/blob/master/HDU1394.cc)
###### [HDU2795](http://acm.hdu.edu.cn/showproblem.php?pid=2795)
一个H * W的木板，把一些1 * L的海报贴上去，问每个海报能贴的最靠上的位置是第几行， 直接每一次找到最大的位置，找到之后将这个位置的线段树记录的大小减去L就好了<br>
具体细节见[HDU2785](https://github.com/edgeOB/source-code/blob/master/HDU2785.cc)

<span id="jump2"></span> 
# 线段树的区间更新与区间查询
