---
layout:     post
title:      "KMP->EX/manacher->Trie->AC"
subtitle:   "对字符串问题进行一次系统的总结"
date:       2018-09-19 09:00:00
author:     "programfiles"
header-img: "img/in-post/kmp_ex_trie_ac.jpg"
tags:
    - 字符串
---

* [KMP](#kmp)<br>
* [E-kmp](#E-kmp)<br>
* [Trie](#trie)<br>
* [Aho-Corasick automaton](#Aho-Corasick-automaton)<br>

>对于字符串问题来说，前缀和后缀的理解尤为重要<br>

<span id="kmp"></span> 
## KMP算法
**参考自** <br>
[CSDN-路漫远吾求索](https://blog.csdn.net/starstar1992/article/details/54913261/){:target="_blank"} <br> 
[CNBLOGS-孤~影](https://www.cnblogs.com/yjiyjige/p/3263858.html#!comments){:target="_blank"} <br>

<kbd>KMP</kbd>：在线性时间内求出字串T在主串S内的所有匹配位置<br>
* * *
**next** 数组表示以主串S中第i个字符 **S[i-1]** 为结尾的后缀与模式子串前缀的最长公共部分，也是模式子串自身与自身匹配的结果，本质上就是一个前缀匹配后缀的算法。<br>
* * *
事实上，简单来说就是 **S[i]==x** 即为S到当前位置与T匹配的字符数量为x(0~x-1), 当 **S[i]!=T[j]** 时，用 **T[nex[i]]==T[x]** 与 **S[i]** 进行匹配看是否成功<br>

**_题型_** <br>
* (持续补充) <br>

<span id="E-kmp"></span> 
## E-KMP算法
**参考自** <br>
[CSDN-dyx心心](https://blog.csdn.net/dyx404514/article/details/41831947#commentBox){:target="_blank"} <br> 

<kbd>EKMP</kbd>：拓展kmp算法通过next数组和extend数组进行模式匹配<br>
**next[i]:x[i...m-1]** 与 **x[0...m-1]** 的最长公共前缀<br>
**extend[i]:y[i...n-1]** 与 **x[0...m-1]** 的最长公共前缀<br>
* * *
<br>
定义母串S，和子串T，设S的长度为n，T的长度为m，求T与S的每一个后缀的最长公共前缀，也就是说，设extend数组,extend[i]表示T与S[i,n-1]的最长公共前缀，要求出所有extend[i] ( 0 <= i < n )。
<br>
* * *
注意到，如果有一个位置extend[i]=m,则表示T在S中出现，而且是在位置i出现，这就是标准的KMP问题，所以说拓展kmp是对KMP算法的扩展，所以一般将它称为扩展KMP算法。<br>

**_题型_** <br>
* (持续补充) <br>

<span id="Trie"></span> 
## 特别好用的Trie树
```cpp
const int maxn = 1000006;
struct Trie
{
    int nex[maxn][26],endd[maxn],num[maxn];
    int root, l;
    int newnode ()
    {
        for(int i=0; i<26; i++)
            nex[l][i] = -1;
        num[l] = 0;
        endd[l++] = 0;
        return l-1;
    }
    void init ()
    {
        l = 0;
        root = newnode();
    }
    void Insert(char buf[])
    {
        int now = root;
        int len = strlen(buf);
        for(int i=0; i<len; i++)
        {
            if(nex[now][buf[i]-'a'] == -1)
            {
                nex[now][buf[i]-'a'] = newnode();
            }
            now = nex[now][buf[i]-'a'];
            num[now]++;
        }
        endd[now]++;
    }
    int query(char buf[])
    {
        int now = root;
        int len = strlen(buf);
        int ans = 0;
        for(int i=0; i<len; i++)
        {
            if(nex[now][buf[i]-'a'] == -1)
            {
                return 0;
            }
            now = nex[now][buf[i]-'a'];
        }
        return num[now];
    }
};
Trie ac;
```
其中26是指如果用小大写字母表的话26，其实是指参与的符号总类数<br>
**最方便的其实是Trie的用法很宽泛，可以在树的节点上加入各种标记/统计数据，树的构造较为简单易于处理** <br>

**_题型_** <br>
* (持续补充) <br>

<span id="Aho-Corasick-automaton"></span> 
## Trie+kmp的合体->Aho-Corasick automaton!

