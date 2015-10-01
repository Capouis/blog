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

* Sorting A Three-Valued Sequence

题意：类似于荷兰三色旗， 给出一些数字， 他们是1，2，3到一种， 现在要求用最小的置换次数， 让这些数字集合按照1，2，3的顺序排列好

分析：既然要求最小的置换次数， 那么1在2的位置上，2在1的位置上这种情况显然可以直接交换， 1在3，3在1， 2在3，3在2上面也是， 而且这样做肯定是最节省交换次数的， 然后剩下的情况只能是：1在2上， 2在3， 3在1的位置上这种情况

```
#include<cstdio>
#include<cstring>
using namespace std;
#define minab(a, b) ((a) < (b) ? (a) : (b))
const int N = 1001;
int a[N];

int main(){
    freopen("sort3.in", "r", stdin);
    freopen("sort3.out", "w", stdout);
    int n;
    scanf("%d", &n);
    int x = 0, y = 0, z = 0;
    int xy = 0, xz = 0, yx = 0, yz = 0, zx = 0, zy = 0;
    for(int i = 0; i < n; ++i){
        scanf("%d", &a[i]);
        if(a[i] == 1){
            ++x;
        }
        if(a[i] == 2){
            ++y;
        }
        if(a[i] == 3){
            ++z;
        }
    }

    for(int i = 0; i < x; ++i){
        if(a[i] == 2){
            ++xy;
        }
        if(a[i] == 3){
            ++xz;
        }
    }
    for(int i = x; i < x+y; ++i){
        if(a[i] == 1){
            ++yx;
        }
        if(a[i] == 3){
            ++yz;
        }
    }
    for(int i = x+y; i < n; ++i){
        if(a[i] == 1){
            ++zx;
        }
        if(a[i] == 2){
            ++zy;
        }
    }
    int ans = minab(xy, yx);
    ans += minab(xz, zx) + minab(yz, zy);
    ans += (xy + yx - 2*minab(xy, yx)) << 1;
    printf("%d\n", ans);
    return 0;
}
```
</br>


