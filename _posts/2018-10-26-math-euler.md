---
layout:     post
title:      "欧拉函数"
subtitle:   "假的欧拉函数"
date:       2018-10-26 14:00:00
author:     "programfiles"
header-img: "img/in-post/euler.jpg"
tags:
    - 数论
---
###### [%ACDREAMER](https://blog.csdn.net/ACdreamers/article/details/8007991)
# 欧拉函数
欧拉函数的定义：**对正整数n，欧拉函数是小于n的正整数中与n互质的数的数目（φ(1)=1）**, 又称为**Euler's totient function**<br>
欧拉函数的性质：<br>
定理一：设m与n是互素的正整数，那么![avatar](/img/blog-small/euler1.jpg)
定理二：当n为奇数时，有![avatar](/img/blog-small/euler2.jpg)
定理三：设p是素数，a是一个正整数，那么![avatar](/img/blog-small/euler3.jpg)证明用到容斥，简单来说就是p^a里面有p^a-1个p的倍数，减去即可<br>
定理四：设![avatar](/img/blog-small/euler4.jpg)为正整数n的素数幂分解，那么![avatar](/img/blog-small/euler5.jpg)可以通过定理13进行简单证明不再写<br>
定理五：设n是一个正整数，那么![avatar](/img/blog-small/euler6.jpg)通过莫比乌斯反演证明<br>
定理六：设m是正整数，(a,m)=1，则：![avatar](/img/blog-small/euler7.jpg)是同于方程的解![avatar](/img/blog-small/euler8.jpg)
定理七：如果n大于2，那么n的欧拉函数值是偶数.<br>
## 求单个数的欧拉函数值
👇基于定理四，O(sqrtn)
```cpp
int phi(int n)
{
    int i, rea = n;
    for(int i = 2; i*i <= n; i++)
        if(n%i == 0)
        {
            rea = rea - rea/i;
            while(n%i == 0) n/=i;
        }
    if(n > 1)
        rea = rea - rea/n;
    return rea;
}
```
## 欧拉函数的筛
利用递推法求欧拉函数值：<br>
算法原理：开始令i的欧拉函数值等于它本身，如果i为偶数，可以利用定理二变为求奇数的。<br>
若p是一个正整数满足 ![avatar](/img/blog-small/euler9.jpg) 那么p是素数，在遍历过程中如果遇到欧拉函数值等于自身的情况，那么<br>
说明该数为素数。把这个数的欧拉函数值改变，同时也把能被该素因子整除的数改变。<br>
```cpp
void phi()
{
    for(int i=1; i<N; i++)  p[i] = i;
    for(int i=2; i<N; i+=2) p[i] >>= 1;
    for(int i=3; i<N; i+=2)
    {
        if(p[i] == i)
        {
            for(int j=i; j<N; j+=i)
                p[j] = p[j] - p[j] / i;
        }
    }
}
```

## 欧拉函数线性筛
利用到几点性质<br>
1.  phi(p) == p-1 因为素数p除了1以外的因子只有p，所以与 p 互素的个数是 p - 1个
2.  phi(p^k) == p^k - p^(k-1) == (p-1) * p^(k-1)
3.  如果i mod p == 0, 那么 phi(i * p) == p * phi(i)
4.  如果i mod p != 0, 那么 phi(i * p) == phi(i) * (p-1) <br>
证明：i mod p 不为0且p为质数, 所以i与p互质, 那么根据积性函数的性质 phi(i * p) == phi(i) * phi(p) 其中phi(p) == p-1

```cpp
const int MAXN = 1e6+5;
bool flag[MAXN];///标记数组

int phi[MAXN];///欧拉函数值，i的欧拉函数值=phi[i]

int p[MAXN];///素因子的值

int cnt = 0;
void Get_phi()///筛法求欧拉函数

{
    cnt = 0;
    memset(flag, true, sizeof(flag));
    phi[1] = 1;
    for(int i=2; i<MAXN; i++)///线性筛法

    {
        if(flag[i])///素数

        {
            p[cnt++] = i;
            phi[i] = i-1;///素数的欧拉函数值是素数 - 1

        }
        for(int j=0; j<cnt; j++)
        {
            if(i*p[j] > MAXN)
                break;
            flag[i*p[j]] = false;///素数的倍数，所以i*p[j]不是素数

            if(i%p[j] == 0)///性质：i mod p == 0, 那么 phi(i * p) == p * phi(i)

            {
                phi[i*p[j]] = p[j] * phi[i];
                break;
            }
            else
                phi[i*p[j]] = (p[j]-1) * phi[i];///i mod p != 0, 那么 phi(i * p) == phi(i) * (p-1)

        }
    }
}
```

### [lightoj1370-Bi-shoe and Phi-shoe](https://vjudge.net/problem/LightOJ-1370)
这道题其实算不上欧拉函数题，有一个数x，找到一个数的因子个数大于等于x，很明显是x之后紧邻的下一个素数，现在给出一堆x，找到每一个x的满足题意的数求和即可
