---
layout:     post
title:      "后缀数组/后缀树/后缀自动机"
subtitle:   "总结字符串的另一部分处理问题"
date:       2018-09-23 09:00:00
author:     "programfiles"
header-img: "img/in-post/suffix.jpg"
tags:
    - 字符串
---
**本文参考自**
* 算法竞赛入门经典 训练指南
* 2009年国家集训队论文 《后缀数组——处理字符串的有力工具》—— 罗穗骞
* [CNBLOGS ~victorique~ 后缀数组最详细讲解](https://www.cnblogs.com/victorique/p/8480093.html#autoid-1-2-0){:target="_blank"}
* [后缀数组 百度百科](https://baike.baidu.com/item/后缀数组/8989867?fr=aladdin#3){:target="_blank"}
* [基数排序 百度百科](https://baike.baidu.com/item/基数排序/7875498?fr=aladdin){:target="_blank"}
* [CNBLOGS 如果天空不死 基数排序](https://www.cnblogs.com/skywang12345/p/3603669.html){:target="_blank"}
* [CNBLOGS kuangbin POJ1743](https://www.cnblogs.com/kuangbin/archive/2013/04/23/3039313.html){:target="_blank"}

>后缀数组磕了好久啊。。我太菜了<br>

# 1.后缀数组
&ensp;&ensp;&ensp;&ensp;后缀数组是处理字符串的有力工具。后缀数组是后缀树的一个非常精巧的
替代品，它比后缀树容易编程实现，能够实现后缀树的很多功能而时间复杂度也
并不逊色，而且它比后缀树所占用的内存空间小很多<br>
其中<br>**倍增算法** 仅需二十多行 ， **DC3** 仅需要四十多行<br>
## 1.1 DA

### 1.1.1 首先是倍增算法
倍增算法利用了 ①后缀在字符串中的应用 ②基数排序(桶排序+)<br>
* 基数排序<br>
"分配式排序"的一种，是桶排序的拓展，时间复杂度为O(n*digit), digit是待排序的数字的位数<br>
* 基于后缀在字符串中的应用的倍增算法原理<br>
**主要思路**是：用倍增的方法对每个字符开始的长度为 2k 的子字符串进行排序，求出排名，即 rank 值。k 从 0 开始，每次加 1，当 2k 大于 n 以后，每个字符开始的长度为 2k 的子字符串便相当于所有的后缀。并且这些子字符串都一定已经比较出大小，即 rank 值中没有相同的值，那么此时的 rank 值就是最后的结果。每一次排序都利用上次长度为 2k-1的字符串的 rank 值，那么长度为 2k 的字符串就可以用两个长度为 2k-1的字符串的排名作为关键字表示，然后进行基数排序，便得出了长度为 2k的字符串的 rank 值。以字符串“aabaaaab”为例，整个过程如图 2 所示。其中 x、y 是表示长度为 2k的字符串的两个关键字
![avatar](/img/in-post/sa1.png)

<kbd>罗神完整实现代码</kbd>
```cpp
const int maxn = 1000001;
int wa[maxn],wb[maxn],wv[maxn],ws[maxn];
int cmp(int *r,int a,int b,int l)
{return r[a]==r[b]&&r[a+l]==r[b+l];}
void da(int *r,int *sa,int n,int m)
{
     int i,j,p,*x=wa,*y=wb,*t;
     for(i=0;i<m;i++) ws[i]=0;
     for(i=0;i<n;i++) ws[x[i]=r[i]]++;
     for(i=1;i<m;i++) ws[i]+=ws[i-1];
     for(i=n-1;i>=0;i--) sa[--ws[x[i]]]=i;
     for(j=1,p=1;p<n;j*=2,m=p)
     {
       for(p=0,i=n-j;i<n;i++) y[p++]=i;
       for(i=0;i<n;i++) if(sa[i]>=j) y[p++]=sa[i]-j;
       for(i=0;i<n;i++) wv[i]=x[y[i]];
       for(i=0;i<m;i++) ws[i]=0;
       for(i=0;i<n;i++) ws[wv[i]]++;
       for(i=1;i<m;i++) ws[i]+=ws[i-1];
       for(i=n-1;i>=0;i--) sa[--ws[wv[i]]]=y[i];
       for(t=x,x=y,y=t,p=1,x[sa[0]]=0,i=1;i<n;i++)
       x[sa[i]]=cmp(y,sa[i-1],sa[i],j)?p-1:p++;
     }
     return;
}
int rank[maxn],height[maxn];
void calheight(int *r,int *sa,int n)
{
     int i,j,k=0;
     for(i=1;i<=n;i++) rank[sa[i]]=i;
     for(i=0;i<n;height[rank[i++]]=k)
     for(k?k--:0,j=sa[rank[i]-1];r[i+k]==r[j+k];k++);
     return;
}
int RMQ[maxn];
int mm[maxn];
int best[20][maxn];
void initRMQ(int n)
{
     int i,j,a,b;
     for(mm[0]=-1,i=1;i<=n;i++)
     mm[i]=((i&(i-1))==0)?mm[i-1]+1:mm[i-1];
     for(i=1;i<=n;i++) best[0][i]=i;
     for(i=1;i<=mm[n];i++)
     for(j=1;j<=n+1-(1<<i);j++)
     {
       a=best[i-1][j];
       b=best[i-1][j+(1<<(i-1))];
       if(RMQ[a]<RMQ[b]) best[i][j]=a;
       else best[i][j]=b;
     }
     return;
}
int askRMQ(int a,int b)
{
    int t;
    t=mm[b-a+1];b-=(1<<t)-1;
    a=best[t][a];b=best[t][b];
    return RMQ[a]<RMQ[b]?a:b;
}
int lcp(int a,int b)
{
    int t;
    a=rank[a];b=rank[b];
    if(a>b) {t=a;a=b;b=t;}
    return(height[askRMQ(a+1,b)]);
}

```
<kbd>bin神模板的实现代码</kbd>
```cpp
const int MAXN=20010;
int t1[MAXN],t2[MAXN],c[MAXN];
bool cmp(int *r,int a,int b,int l)
{return r[a] == r[b] && r[a+l] == r[b+l];}
void da(int str[],int sa[],int rank[],int height[],int n,int m)
{
    n++;
    int i, j, p, *x = t1, *y = t2;   
    for(i = 0;i < m;i++)c[i] = 0;
    for(i = 0;i < n;i++)c[x[i] = str[i]]++;
    for(i = 1;i < m;i++)c[i] += c[i-1];
    for(i = n-1;i >= 0;i--)sa[--c[x[i]]] = i;
    for(j = 1;j <= n; j <<= 1)
    {
        p = 0;  
        for(i = n-j; i < n; i++)y[p++] = i;
        for(i = 0; i < n; i++)if(sa[i] >= j)y[p++] = sa[i] - j;  
        for(i = 0; i < m; i++)c[i] = 0;
        for(i = 0; i < n; i++)c[x[y[i]]]++;
        for(i = 1; i < m;i++)c[i] += c[i-1];
        for(i = n-1; i >= 0;i--)sa[--c[x[y[i]]]] = y[i];
        swap(x,y);
        p = 1; x[sa[0]] = 0;
        for(i = 1;i < n;i++)
        x[sa[i]] = cmp(y,sa[i-1],sa[i],j)?p-1:p++;
        if(p >= n)break;
        m = p;
    }
    int k = 0;
    n--;
    for(i = 0;i <= n;i++)rank[sa[i]] = i;
    for(i = 0;i < n;i++)
    {
        if(k)k--;
        j = sa[rank[i]-1];
        while(str[i+k] == str[j+k])k++;
        height[rank[i]] = k;
    }
}
int rank[MAXN],height[MAXN];
int RMQ[MAXN];
int mm[MAXN];
int best[20][MAXN];
void initRMQ(int n)
{
    mm[0]=-1;
    for(int i=1;i<=n;i++)
    mm[i]=((i&(i-1))==0)?mm[i-1]+1:mm[i-1];
    for(int i=1;i<=n;i++)best[0][i]=i;
    for(int i=1;i<=mm[n];i++)
    for(int j=1;j+(1<<i)-1<=n;j++)
    {
        int a=best[i-1][j];
        int b=best[i-1][j+(1<<(i-1))];
        if(RMQ[a]<RMQ[b])best[i][j]=a;
        else best[i][j]=b;
    }
}
int askRMQ(int a,int b)
{
    int t;
    t=mm[b-a+1];
    b-=(1<<t)-1;
    a=best[t][a];b=best[t][b];
    return RMQ[a]<RMQ[b]?a:b;
}
int lcp(int a,int b)
{
    a=rank[a];b=rank[b];
    if(a>b)swap(a,b);
    return height[askRMQ(a+1,b)];
}
char str[MAXN];
int r[MAXN];
int sa[MAXN];
int main()
{
    while(scanf("%s",str) == 1)
    {
        int len = strlen(str);
        int n = 2*len + 1;
        for(int i = 0;i < len;i++)r[i] = str[i];
        for(int i = 0;i < len;i++)r[len + 1 + i] = str[len - 1 - i];
        r[len] = 1;
        r[n] = 0;
        da(r,sa,rank,height,n,128);
        for(int i=1;i<=n;i++)RMQ[i]=height[i];
        initRMQ(n);
        int ans=0,st;
        int tmp;
        for(int i=0;i<len;i++)
        {
            tmp=lcp(i,n-i);
            if(2*tmp>ans)
            {
                ans=2*tmp;
                st=i-tmp;
            }
            tmp=lcp(i,n-i-1);
            if(2*tmp-1>ans)
            {
                ans=2*tmp-1;
                st=i-tmp+1;
            }
        }
        str[st+ans]=0;
        printf("%s\n",str+st);
    }
    return 0;
}

```
<br>
### 1.1.2部分代码解释
**第一轮基数排序**
```cpp
for(i=0;i<m;i++)c[i]=0;
for(i=0;i<n;i++)c[x[i]=s[i]]++;
for(i=1;i<m;i++)c[i]+=c[i-1];
for(i=n-1;i>=0;i--)sa[--c[x[i]]]=i;
```
最后一行正常的基数排序应该写成
```cpp
for(i=n-1;i>=0;i--)sa[--c[x[i]]]=x[i];
```
事实上<br>
是因为sa[ i ]表示第排名第i的后缀的起始位置是sa[ i ];<br>
* * *
**直接利用sa数组排序第二关键字**
基数排序要分两次，第一次是对第二关键字排序，第二次是对第一关键字排序。对第二关键字排序的结果实际上可以利用上一次求得的 sa 直接算出，没有必要再算一次
```cpp
for(p=0,i=n-j;i<n;i++)y[p++]=i;
for(i=0;i<n;i++)if(sa[i]>=j)y[p++]=sa[i]-j;
```
其中变量j是当前字符串的长度，数组y保存的是对第二关键字排序的结果<br>
然后要对第一关键字进行排序
```cpp
for(i=0;i<m;i++)c[i]=0;
for(i=0;i<n;i++)c[x[y[i]]]++;
for(i=1;i<m;i++)c[i]+=c[i-1];
for(i=n-1;i>=0;i--)sa[--c[x[y[i]]]]=y[i];
```
* * *
**用指针类型定义x和y**
先来了解一下，rank数组是还未谈到的一种数组，它的值是sa的逆向值，即sa[rank[i]] = rank[sa[i]]<br>
可以解释为：**后缀数组（SA）是 “ 排第几的是谁？ ” ，名次数组（RANK）是 “ 你排第几？ ”**<br>
在求出 sa 后，下一步是计算 rank 值。这里要注意的是，在未完全处理完之前，可能有多个字符串的 rank 值是相同的，所以必须比较两个字符串是否完全相同，y 数组的值已经没有必要保存，为了节省空间，这里用 y 数组保存 rank值。这里又有一个小优化，将 x 和 y 定义为指针类型，复制整个数组的操作可以用交换指针的值代替，不必将数组中值一个一个的复制。
```cpp
for(i=1;i<n;i++)
    x[sa[i]]=y[sa[i-1]]==y[sa[i]] && y[sa[i-1]+j]==y[sa[i]+j]?p-1:p++;
```
**p和n**
执行完上面的代码后，rank值保存在 x 数组中，而变量 p 的结果实际上就是不同的字符串的个数。这里可以加一个小优化，如果 p 等于 n，那么函数可以结束。因为在当前长度的字符串中 ，已经没有相同的字符串，接下来的排序不会改变 rank 值。

## 1.2 DC3
<kbd>罗神论文代码</kbd>
```cpp
const manx = 1000003;  
#define F(x) ((x)/3+((x)%3==1?0:tb))  

#define G(x) ((x)<tb?(x)*3+1:((x)-tb)*3+2) 

int wa[maxn],wb[maxn],wv[maxn],ws[maxn];

int c0(int *r,int a,int b)
{return r[a]==r[b]&&r[a+1]==r[b+1]&&r[a+2]==r[b+2];}

int c12(int k,int *r,int a,int b)
{if(k==2) return r[a]<r[b]||r[a]==r[b]&&c12(1,r,a+1,b+1);
 else return r[a]<r[b]||r[a]==r[b]&&wv[a+1]<wv[b+1];}

void sort(int *r,int *a,int *b,int n,int m)
{
     int i;
     for(i=0;i<n;i++) wv[i]=r[a[i]];
     for(i=0;i<m;i++) ws[i]=0;
     for(i=0;i<n;i++) ws[wv[i]]++;
     for(i=1;i<m;i++) ws[i]+=ws[i-1];
     for(i=n-1;i>=0;i--) b[--ws[wv[i]]]=a[i];
     return;
}
void dc3(int *r,int *sa,int n,int m)
{
     int i,j,*rn=r+n,*san=sa+n,ta=0,tb=(n+1)/3,tbc=0,p;
     r[n]=r[n+1]=0;
     for(i=0;i<n;i++) if(i%3!=0) wa[tbc++]=i;
     sort(r+2,wa,wb,tbc,m);
     sort(r+1,wb,wa,tbc,m);
     sort(r,wa,wb,tbc,m);
     for(p=1,rn[F(wb[0])]=0,i=1;i<tbc;i++)
     rn[F(wb[i])]=c0(r,wb[i-1],wb[i])?p-1:p++;
     if(p<tbc) dc3(rn,san,tbc,p);
     else for(i=0;i<tbc;i++) san[rn[i]]=i;
     for(i=0;i<tbc;i++) if(san[i]<tb) wb[ta++]=san[i]*3;
     if(n%3==1) wb[ta++]=n-1;
     sort(r,wb,wa,ta,m);
     for(i=0;i<tbc;i++) wv[wb[i]=G(san[i])]=i;
     for(i=0,j=0,p=0;i<ta && j<tbc;p++)
     sa[p]=c12(wb[j]%3,r,wa[i],wb[j])?wa[i++]:wb[j++];
     for(;i<ta;p++) sa[p]=wa[i++];
     for(;j<tbc;p++) sa[p]=wb[j++];
     return;
}
int rank[maxn],height[maxn];
void calheight(int *r,int *sa,int n)
{
     int i,j,k=0;
     for(i=1;i<=n;i++) rank[sa[i]]=i;
     for(i=0;i<n;height[rank[i++]]=k)
     for(k?k--:0,j=sa[rank[i]-1];r[i+k]==r[j+k];k++);
     return;
}
int RMQ[maxn];
int mm[maxn];
int best[20][maxn];
void initRMQ(int n)
{
     int i,j,a,b;
     for(mm[0]=-1,i=1;i<=n;i++)
     mm[i]=((i&(i-1))==0)?mm[i-1]+1:mm[i-1];
     for(i=1;i<=n;i++) best[0][i]=i;
     for(i=1;i<=mm[n];i++)
     for(j=1;j<=n+1-(1<<i);j++)
     {
       a=best[i-1][j];
       b=best[i-1][j+(1<<(i-1))];
       if(RMQ[a]<RMQ[b]) best[i][j]=a;
       else best[i][j]=b;
     }
     return;
}
int askRMQ(int a,int b)
{
    int t;
    t=mm[b-a+1];b-=(1<<t)-1;
    a=best[t][a];b=best[t][b];
    return RMQ[a]<RMQ[b]?a:b;
}
int lcp(int a,int b)
{
    int t;
    a=rank[a];b=rank[b];
    if(a>b) {t=a;a=b;b=t;}
    return(height[askRMQ(a+1,b)]);
}
```

# 2.后缀数组的应用！
#### 2.0.1 HEIGHT数组
**height 数组：定义 height[i]=suffix(sa[i-1])和 suffix(sa[i])的最长公共前缀，也就是排名相邻的两个后缀的最长公共前缀。**<br>
与之相关最重要的就是 **最长公共前缀LCP——后缀数组的辅助工具** 
#### 2.0.2 什么是LCP？
我们定义LCP(i,j)为suff(sa[i])与suff(sa[j])的最长公共前缀
#### 2.0.3 关于LCP的几条性质
<kbd>LCP(i,j)=LCP(j,i);</kbd><br>
<kbd>LCP(i,i)=len(sa[i])=n-sa[i]+1;</kbd><br>
对于i>j的情况，我们可以把它转化成i < j，对于i==j的情况，我们可以直接算长度，所以我们直接讨论i < j的情况就可以了。我们每次依次比较字符肯定是不行的，单次复杂度为O(n)，太高了，所以我们要做一定的预处理才行。<br>
* LCP Lemma<br>
<kbd>LCP(i,k)=min(LCP(i,j),LCP(j,k)) 对于任意1<=i<=j<=k<=n</kbd><br>
* LCP Theorem<br>
<kbd>LCP(i,k)=min(LCP(j,j-1)) 对于任意1< i<= j<= k<= n</kbd>

#### 2.0.4 怎么求LCP？
<kbd>height[rk[i]]>=height[rk[i-1]]-1，也即h[i]>=h[i-1]-1</kbd>
```cpp
int rank[maxn],height[maxn];
void calheight(int *r,int *sa,int n)
{
    int i,j,k=0;
    for(i=1;i<=n;i++) rank[sa[i]]=i;
    for(i=0;i<n;height[rank[i++]]=k)
    for(k?k--:0,j=sa[rank[i]-1];r[i+k]==r[j+k];k++);
    return;
}
```
上面是罗神的代码，不过我还是觉得bin神的代码比较简单易懂，在下面的题目类型种放出

## 2.1 最长公共前缀
&ensp;&ensp;&ensp;&ensp;给定一个字符串，询问某两个后缀的最长公共前缀。<br>
&ensp;&ensp;&ensp;&ensp;算法分析：<br>
&ensp;&ensp;&ensp;&ensp;按照上面所说的做法，求两个后缀的最长公共前缀可以转化为求某个区间上<br>
的最小值。对于这个 RMQ 问题（如果对 RMQ 问题不熟悉，请阅读其他相关资料），可以用 O(nlogn)的时间先预处理，以后每次回答询问的时间为 O(1)。所以对于本问题，预处理时间为 O(nlogn)，每次回答询问的时间为 O(1)。如果 RMQ 问题用 O(n)的时间预处理，那么本问题预处理的时间可以做到 O(n)。 <br>
* [POJ 1743](http://poj.org/problem?id=1743){:target="_blank"} <br>

* [POJ 3261](http://poj.org/problem?id=3261){:target="_blank"} <br>
后缀数组基础题，加上了二分算法（二分算法牛逼）
```cpp
int binary(int n, int m, int k)
{
    int cur = 1;
    for(int i = 2; i <= n; i++){
        if(height[i] >= k) cur++;
        else               cur = 1;
        if(cur >= m)       return 1;
    }
    return 0;
}
```

## 2.2 单个字符串的相关问题
### 2.2.1 重复子串
### 2.2.2 子串的个数

* [SPOJ DISUBSTR](https://www.spoj.com/problems/DISUBSTR/en/){:target="_blank"} <br>
height后缀应用题，在论文中的解释：<br>
给定一个字符串，求不相同的子串的个数。算法分析：每个子串一定是某个后缀的前缀，那么原问题等价于求所有后缀之间的不相同的前缀的个数。如果所有的后缀按照 suffix(sa[1]), suffix(sa[2]),suffix(sa[3]), …… ,suffix(sa[n])的顺序计算，不难发现，对于每一次新加进来的后缀 suffix(sa[k]),它将产生 n-sa[k]+1 个新的前缀。但是其中有height[k]个是和前面的字符串的前缀是相同的。所以 suffix(sa[k])将“贡献”出 n-sa[k]+1- height[k]个不同的子串。累加后便是原问题的答案。这个做法的时间复杂度为 O(n)。
**注意越界问题，m要比c（桶排序）数组的大小要小一点（来自-7的泪目）**

### 2.2.3 回文子串
### 2.2.4 连续重复子串
## 2.3 两个字符串的相关问题
### 2.3.1 公共子串
### 2.3.2 子串的个数
## 2.4 多个字符串的相关问题