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

接下来是线段树的区间更新与区间查询，区间更新需要用到延迟标记的方法来进行，因为每一次更新都更新到线段树的底部所用的时间太多，所谓延迟标记的方法也就是每一次更新区间信息的时候不必更新到底部，我们只需要用到哪里更新到哪里，比如：![avatar](/img/blog-small/ACM-DataStructure1.png)<br>
我们需要在[1,16]的线段上更新[5,8]，线段树查找的路径分别是紫色线，蓝色线和红色线，如果是传统的单点更新这个时候就会将4个点均向下递归更新，但是我们其实更新到这一步只需要标记col[rt]为"需要更新"即可，无需再向下找了<br><br>
同样这里放置一道简单的线段树区间更新问题样板
###### [POJ3468](http://poj.org/problem?id=3468)
这道题进行区间增加，和查找区间和的操作，线段树最主要的还是理解push_up和push_down这两个函数，大多数细节都是在这两个函数上修改的，update和query函数进行相似的修改
基础函数如下，[代码细节](https://github.com/edgeOB/source-code/blob/master/POJ3468.cc)
```cpp
void push_up(int rt) {
    tree[rt] = tree[rt<<1] + tree[rt<<1|1];
}
void push_down(int rt, int l) {
    if(col[rt]) {
        col[rt<<1] += col[rt];
        col[rt<<1|1] += col[rt];
        tree[rt<<1] += (l - (l >> 1)) * col[rt];
        tree[rt<<1|1] += (l >> 1) * col[rt];
        col[rt] = 0;
    }
}
void build(int l, int r, int rt) {
    col[rt] = 0;
    if(l == r) {
        scanf("%lld", &tree[rt]);
        return;
    }
    int m = (l+r)>>1;
    build(lson);
    build(rson);
    push_up(rt);
}
void update(int L, int R, int c, int l, int r, int rt) {
    if(L <= l && R >= r) {
        col[rt] += c;
        tree[rt] += (ll)c*(r-l+1);
        return;
    }
    push_down(rt, r-l+1);
    int m = (l+r)>>1;
    if(L <= m) update(L, R, c, lson);
    if(R > m) update(L, R, c, rson);
    push_up(rt);
}
ll query(int L, int R, int l, int r, int rt) {
    if(L <= l && R >= r) {
        return tree[rt];
    }
    push_down(rt, r-l+1);
    int m = (r+l)>>1;
    ll ret = 0;
    if(L <= m) ret += query(L, R, lson);
    if(R > m) ret += query(L, R, rson);
    return ret;
}
```
###### [POJ2528](http://poj.org/problem?id=2528)
[代码](https://github.com/edgeOB/source-code/blob/master/POJ2528.cc)<br>
题意：区间覆盖，每次将一个区间的数进行修改，最后统计这个区间总计有多少个不同的数<br>
分析：首先这道题数字范围过大，我们需要进行一些离散化处理，我们这里采用数组+排序的方法，将每一个数字映射为在排序并去重后的数组中的位置，其次这里在离散化的时候还有一些细节如<br>
1 10, 1 4, 5 10<br>
1 10, 1 4, 6 10<br>
这两个区间通常的离散化之后结果是相同的1 4, 1, 2, 3, 4，但是明显这样会使第二个线段缺少一个区间5，为了解决这种缺陷,我们可以在排序后的数组上加些处理,比如说[1,2,6,10] <br> 如果相邻数字间距大于1的话,在其中加上任意一个数字,比如加成[1,2,3,6,7,10],然后再做线段树就好了. <br>

###### [POJ3225](http://poj.org/problem?id=3225)
[代码](https://github.com/edgeOB/source-code/blob/master/POJ3225.cc)<br>
区间的交并补等等。。神仙题一个，今天遇到，不出意料的崩了，，<br><br>

这个题意我是看了好多遍才差不多看懂的，我们来仔细分析一下每一个字母代表的操作：<br>
先转化成0/1线段树的问题，然后我们可以发现<br>
U:把区间[l,r]覆盖成1<br>
I:把[-inf,l) (r,inf]覆盖成0<br>
D:把区间[l,r]覆盖成0<br>
C:把[-inf,l) (r,inf]覆盖成0 , 且[l,r]区间0/1互换<br>
S:[l,r]区间0/1互换<br>
我们总结出两个操作，区间覆盖与区间异或
<br><br>

那么下一步是处理区间问题，我们该怎么来表示开闭区间的问题呢，这里有一个简单的解决办法，我们将区间左右边界都 乘2，然后根据开闭来+-1的修改区间边界<br>
(3, 4) -> (6+1, 8-1)<br>
(3, 4] -> (6+1, 8]<br>
[3, 4) -> [6, 8-1)<br>
[3, 4] -> [6, 8]<br>
这样的方式，然后我们可以发现奇数均代表开区间，偶数代表闭区间，最后结果的输出重新除以2输出即可<br><br>
其他需要注意的细节标注在代码中了，这道题挖坑以后重新来写，今天看了标程才写出来，，鶸の叹息😭

<span id="jump3"></span> 
# 线段树的区间合并