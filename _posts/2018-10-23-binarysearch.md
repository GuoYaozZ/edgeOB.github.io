---
layout:     post
title:      "二分查找"
subtitle:   "弥补基础的练习，二分的一些小应用"
date:       2018-10-23 23:00:00
author:     "programfiles"
header-img: "img/in-post/binary.jpg"
tags:
    - 数学
---
**做了一些二分题，感受最深的是，二分其实就是穷举方法的一种优化方式，本质是作用在有序的查找区间上面来缩短时间的一种高效的方法。这样的话也可以用到一些特别的地方，例如最大化最小值，或者最小化最大值**<br>

## [poj1064](http://poj.org/problem?id=1064)
一堆电缆n条长短不一，要求找到一个最长长度，按照这个长度来截取电缆可以得到k条一样的长度的电缆<br>
二分电缆的最大长度，思路很简单，有一些需要注意的地方，这个题求到满足条件的长度之后需要将长度*100然后向下取整，这里用mid满足条件，向下取整可能会变小，所以用r向下取整
```cpp
double a[100003];
int main()
{
    int n,k;
    while(scanf("%d%d",&n,&k)!=EOF)
    {
        for(int i = 0; i < n; i++)
            scanf("%lf", &a[i]);
        double l = 0.0, r = 1000000.0, mid;
        while(r - l > 0.001)
        {
            mid = (l + r) / 2.0;
            int sum = 0;
            for(int i = 0; i < n; i++)
                sum += int(a[i]/mid);
            if(sum >= k)
                l = mid;
            else
                r = mid;
        }
        printf("%.2f\n", floor(r*100)/100);
    }
    return 0;
}
```
## [hdu4430](http://acm.hdu.edu.cn/showproblem.php?pid=4430)
最大值1e12，通过等比序列求和公式，我们知道指数不会超过40，所以我们可以枚举质数r，二分底数k，计算的过程中需要注意一下溢出问题
```cpp
ll n;
ll bin(ll s)
{
    ll l = 2,r = n,mid,i;
    while(l<=r)//二分k

    {
        mid = (l+r)/2;
        ll sum = 1,ans = 0;;
        for(i = 1; i<=s; i++)
        {
            if(n/sum<mid)//不足以堆下一次

            {
                ans = n+1;
                break;
            }
            sum*=mid;
            ans+=sum;
            if(ans>n)//溢出

                break;
        }
        if(ans == n || ans == n-1) return mid;
        else if(ans<n-1) l = mid+1;
        else r = mid-1;
    }
    return -1;
}
int main()
{
    ll s,k,l,r,mid,i;
    while(~scanf("%I64d",&n))
    {
        l = 1,r = n-1;
        for(i = 2; i<=45; i++)//枚举i
        {
            k = bin(i);
            if(k!=-1 && i*k<l*r)
            {
                l = i,r = k;
            }
        }
        printf("%I64d %I64d\n",l,r);
    }
    return 0;
}
```
## [poj3273](http://poj.org/problem?id=3273)
给出农夫在n天中每天的花费，要求把这n天分作m组，每组的天数必然是连续的，要求分得各组的花费之和应该尽可能地小，最后输出各组花费之和中的最大值<br>
利用二分查找最小化最大值的经典问题<br>
**求最大化最小值或者最小化最大值的问题可以用二分法来做。为什么用二分？ 以求最大化最小值问题为例。 我们知道二分有一个前提就是对有序的东西才能二分查找。也就是这个问题变量是有序的才能用二分。 在求最大化最小值时，既然求值，那么这个值一定有一个范围。而求最大化最小值时。既然它可以“最大化”，那我们一般可以推到一个”最大值“。既然它可以“最小值”，那我们一般可以推到一个”最小值“。可知最终的这个“值”一定在这个最大值与最小值之间的范围里。当我们在这个范围里挑一个值得时候。我们经过处理，得出一个和题目要求位置相同的变量。我们挑的这个值确定的这个变量状态与题目的要求变量进行比较。这个就是有序的。在本题中就是，但我枚举一个总和k后，求得在这个中和下的最大分段数，这个分段数与题目要求的分m段的比较是有序的。如果比m大，说明段数分多了–>说明k枚举小了，接下来再在大于k的区间枚举。这就可以二分了。再就是唯一性问题。 一般而言最大化最小值问题都会只有一个确定的答案（如果有多个答案，也应该都是一样的值）这是显然的。那么在二分过程中我们应该是不断缩小[L，R]的范围，最终使得L==R才退出二分。这样才是一个唯一的结果。如果当我们过程中查到了某个值的状态满足题目要求就退出。我们就无法保证这是唯一的那个答案。**

```cpp
int a[100003], sum = 0, n, m, l = 0, r, mid;
bool check(int mid)
{
    int group = 1, aum = 0;
    for(int i = 0; i < n; i++)
    {
        if(aum + a[i] > mid)
        {
            group++;
            aum = a[i];
        }
        else aum += a[i];
    }
    if(group <= m) return true;
    else return false;
}
int main()
{
    ios::sync_with_stdio(false);
    while(cin >> n >> m)
    {
        for(int i = 0; i < n; i++)
        {cin >> a[i];sum += a[i];l = max(l, a[i]);}
        r = sum;
        while(l <= r)
        {
            mid = (l+r) / 2;
            if(check(mid))
                 r = mid - 1;
            else l = mid + 1;
        }
        cout << mid << endl;
    }
    return 0;
}
```