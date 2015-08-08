---
layout: default
title: USACO-Chapter Two Solution
comments: true
---

---
* The Castle

题意: 一个城堡的房间构成了m*n的矩阵, 其中有的房间是隔着墙的有的则没有. 首先要求城堡可以由墙分成多少个联通块. 如果可以推倒其中一面墙, 问推倒哪面墙可以使得新的联通块的最大的空间的值最大.

分析: 经典'floodfill'问题, 也就是用dfs求联通块.  注意在寻找推倒的墙面的位置时, 题目要求是: 尽量靠近WEST, 否则尽量靠近SOUTH, 同时, N的优先级要高于E, 所以枚举的时候要注意顺序.

```
/*
ID: geek7774
LANG: C++
TASK: castle
*/

#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int N = 55;

int g[N][N];
bool isWall[N][N][5];
int cnt[N*N];
int t;

void floodfill(int x, int y){
    if(g[x][y] != -1) return;

    g[x][y] = t;
    cnt[t]++;

    if(!isWall[x][y][1]){
        floodfill(x, y-1);
    }

    if(!isWall[x][y][2]){
        floodfill(x-1, y);
    }

    if(!isWall[x][y][3]){
        floodfill(x, y+1);
    }

    if(!isWall[x][y][4]){
        floodfill(x+1, y);
    }
}

int main(){
    freopen("castle.in", "r", stdin);
    freopen("castle.out", "w", stdout);
    int m, n;
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= m; ++i){
        for(int j = 1; j <= n; ++j){
            int k;
            scanf("%d", &k);
            for(int p = 0; p < 4; ++p){
                if(k & (1<<p)){
                    isWall[i][j][p+1] = true;
                }
            }
            g[i][j] = -1;
        }
    }

    t = 0;
    for(int i = 1; i <= m; ++i){
        for(int j = 1; j <= n; ++j){
            if(g[i][j] == -1){
                floodfill(i, j);
                ++t;
            }
        }
    }

    int maxE = 0;
    for(int i = 0; i < t; ++i){
        if(cnt[i] > maxE){
            maxE = cnt[i];
        }
    }
    printf("%d\n%d\n", t, maxE);

    int maxS = 0, maxi, maxj;
    char dir;
    for(int j = 1; j <= n; ++j){
        for(int i = m; i >= 1; --i){
            if(i > 1 && g[i][j] != g[i-1][j]){
                int x = cnt[g[i][j]] + cnt[g[i-1][j]];
                if(x > maxS){
                    maxS = x;
                    maxi = i, maxj = j;
                    dir = 'N';
                }
            }

            if(j < n && g[i][j] != g[i][j+1]){
                int x = cnt[g[i][j+1]] + cnt[g[i][j]];
                if(x > maxS){
                    maxS = x;
                    maxi = i, maxj = j;
                    dir = 'E';
                }
            }
        }
    }

    printf("%d\n%d %d %c\n", maxS, maxi, maxj, dir);
    return 0;
}
```
</br>

* Ordered Fractions

题意: 要求构造介于0/1与1/1之间的所有分数, 满足分数的分母不超过N

分析: 这道题目先构造出所有的分数,然后排序输出.
但是这题很巧妙, 有一个super解法, 也就是递归构造, 至于为什么会这样, 应该是数学的一种美吧.

```
/*
ID: geek7774
LANG: C++
TASK: frac1
*/

#include<cstdio>
#include<cstring>
#include<cmath>
using namespace std;
int n;

void solve(int x1, int y1, int x2, int y2){
    int x = x1 + x2, y = y1 + y2;
    if(y > n){
        return ;
    }
    solve(x1, y1, x, y);
    printf("%d/%d\n", x, y);
    solve(x, y, x2, y2);
}

int main(){
    freopen("frac1.in", "r", stdin);
    freopen("frac1.out", "w", stdout);
    scanf("%d", &n);
    printf("0/1\n");
    solve(0, 1, 1, 1);
    printf("1/1\n");
    return 0;
}
```
</br> 
