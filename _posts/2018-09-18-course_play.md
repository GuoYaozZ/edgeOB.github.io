---
layout:     post
title:      "简单射击游戏-课程设计"
subtitle:   "2018大二入学程序设计课程"
date:       2018-09-18 12:41:00
author:     "programfiles"
header-img: "img/post-bg-1.jpg"
tags:
    - my_design
---
>SHOOOOOOOOOOT<br>

[游戏介绍](#jump1)<br>
[------游戏内容](#jump2)<br>
[------功能实现](#jump3)<br>
[------存在的问题](#jump4)<br>
[源码](#jump5)<br>

<span id="jump1"></span> 
# 游戏介绍
一个简单的射击模拟类游戏<br>
&ensp;&ensp;&ensp;&ensp;最初的意愿是写坦克大战，后来改成了敌人追着玩家跑的那种，用控制台实现界面
&ensp;&ensp;&ensp;&ensp;写的第一个小游戏

<span id="jump2"></span> 
### 游戏内容
上下左右操作玩家移动，IKJL操作玩家上下左右发射子弹，红色敌人，蓝色玩家<br>
做的界面还能看，最起码不算太烂，简洁友好<br>
**初始界面**( ~~彩蛋~~ )
![avatar](/img/in-post/course_play1.png)
```cpp
void to_a_start()
{
    goto_xy(0, 0);  pick_color(15); cout << "NNNNNNNNNow youuu ccaaaaan choooooooossssse toooo ppppreeesss keeeybboarddd" << endl;
                    pick_color(15); cout << "TTTOoOo SSsSStaaartt ttthhiss vvvvvvvvvvvvvvvveeeeeeeerrrryy #lowwwww game#" << endl;
                    pick_color(8);  cout << "Explain text/ for a gameeee://////////important!! copywrong:: inceptions.me" << endl;
                                    cout << endl;
                                    cout << "xuezwohuiquzixikaolvlliangt yehaishijuedingjujuenidezhuiq feichangganxienid" << endl;
                                    cout << "uiyigxinshengdebang!zhu haimeiruxuejiujialwodeqq yizhigeiwoshuozenmeyangzen" << endl;
                                    cout << "meyang suiranbuzhidaowozhangshenmyangzidanhaishipeiwoliaodaohenwan xiexieni" << endl;
                                    cout << "qianjitianjiewohaiqingwochifan lingchensandianyehuidadianhuagwoshuonihenjim" << endl;
                                    cout << "danwobijingcaidayigangjinxuexiao nisongwodedongxiwohuihuangeinide yexiwangn" << endl;
                                    cout << "haohaoxuexi buyaozaigeiwofaduanxinqqdianhuaweixinl xiexiexuezhangnishighaor" << endl;
    goto_xy(0, N-10);pick_color(15);cout << "START GAME BY PRESSING A" << endl;
    goto_xy(0, N-5); pick_color(15);cout << "EASY: e/E                  MID:m/M                    HARD:h/H"       << endl;
                                    cout << endl;
    return ;
}
```
**结束界面**
```cpp
system("cls");
cout << "   IT'S OVER!5 seconds to choose..." << endl;
cout << "GET BACK AGAIN? if so please wait 5 sec to build the game" << endl;
```

<span id="jump3"></span> 
### 功能实现
说说几点重la要ji的功能实现<br>
* 输出<br>
首先是主函数开始输出初始界面switch&case选择难度进入游戏界面<br>
因为数据量较小，我将程序所有的输出集中到了同一个函数 *check_maze* 中，通过调用<kbd>#include"thread"</kbd>为程序的输出单独创建了一个线程并写下死循环令其独立运行从而实现边读入边输出
```cpp
thread task(check_maze);
task.detach();
```
* 移动<br>
为了防止闪屏，通过覆盖的方式实现角色移动，即下一个位置输出图形，当前位置用地板覆盖<br>
![avatar](/img/in-post/course_play2.png)
移动通过调用WINDOWS API的COORD封装类型实现在控制台上的坐标移动，其次是图形颜色<br>
```cpp
void goto_xy(int x, int y)
{
    COORD pos; x+y
    pos.X = 2*x;
    pos.Y = y;
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), pos);
}
void pick_color(int a)
{
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), a);
}
```
* 敌人移动<br>
每隔一段时间给每一个敌人分配一个路径，该路径由广搜简单实现，路径为敌人指向玩家，路程上会选择避开玩家的子弹的路径<br>
```cpp
int vis[N][N];
void ai_for_enemy_bfs(int x, int y)
{
    memset(vis, 0, sizeof vis);
    memset(enemy_changed, 0, sizeof enemy_changed);
    int enemy_value2 = enemy_value;
    queue<place> Q;
    place nowing;
    nowing.a = 0;
    nowing.b = 0;
    nowing.x = x;
    nowing.y = y;
    vis[x][y] = 1;
    Q.push(nowing);
    while(!Q.empty())
    {
        nowing = Q.front();
        Q.pop();
        int k = picture[nowing.x][nowing.y] - 1;
        if(k >= 0 && enemy_changed[k] == 0 && enemy_killed[k] == 0)
        {
            your_enemy_place[k].a = your_enemy_place[k].x;
            your_enemy_place[k].b = your_enemy_place[k].y;
            your_enemy_place[k].x = nowing.a;
            your_enemy_place[k].y = nowing.b;
            enemy_value2 -- ;
            enemy_changed[k] = 1;
            if(enemy_value2 == 0)
                return ;
        }

        place nex;
        for(int i = 0; i < 4; i++)
        {
            nex.x = nowing.x + towards[i][0];
            nex.y = nowing.y + towards[i][1];

            if(picture[nex.x][nex.y] >= 0   &&
               vis[nex.x][nex.y] == 0       &&
               nex.x >= 1                   &&
               nex.x <= N                   &&
               nex.y >= 1                   &&
               nex.y <= N                   )
            {
                nex.a = nowing.x;
                nex.b = nowing.y;
                Q.push(nex);
                vis[nex.x][nex.y] = 1;
            }
        }
    }
}
```

<span id="jump4"></span> 
### 存在的问题们
* * *
发射大量子弹的时候子弹会停下，敌人会不动？
* * *
时而能够从地图右上穿墙而出从地图左侧进入
* * *
时而刷新不出敌人
* * *
因为刷新频率的原因留下的分身
* * *
运行时偶然的崩溃
* * *
**因为时间原因和比赛临近，这些问题留待以后解决吧**

###### 源码位置
<br>
[github.com/edgeOB](https://github.com/edgeOB" 简单射击"){:target="_blank"}