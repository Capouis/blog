---
layout: default
title: USACO-Chapter Three Solution
comments: true
---

* Agri-Net

题意：给出图的邻接矩阵， 求最小生成树

分析：求MST最常用的方法是prim ｜ kruskal， 我习惯用kruskal

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int N = 101;
int maze[N][N], fa[N];
int n, m;

struct node{
    int x, y, z;
}a[N*N];

bool cmp(const node &x, const node &y){
    return x.z < y.z;
}

int find(int x){
    if(fa[x] != x){
        fa[x] = find(fa[x]);
    }
    return fa[x];
}

void solve(){
    sort(a, a+m, cmp);
    int ans = 0, cnt = 0;
    for(int i = 0; i < m; ++i){
        int fx = find(a[i].x);
        int fy = find(a[i].y);
        if(fx != fy){
            fa[fx] = fy;
            ans += a[i].z;
            ++cnt;
            if(cnt == n-1){
                printf("%d\n", ans);
                return ;
            }
        }
    }
}

int main(){
    freopen("agrinet.in", "r", stdin);
    freopen("agrinet.out", "w", stdout);
    scanf("%d", &n);
    m = 0;
    for(int i = 0; i < n; ++i){
        for(int j = 0; j < n; ++j){
            scanf("%d", &maze[i][j]);
            if(i < j && maze[i][j]){
                a[m].x = i, a[m].y = j, a[m++].z = maze[i][j];
            }
        }
    }
    for(int i = 0; i < n; ++i){
        fa[i] = i;
    }
    solve();
    return 0;
}
```
</br>

* A Game

题意：两个人玩一个取牌的游戏， 一堆牌排成一列，每张牌都有一个值， 现在两个人每次可以从两头中的任意一头抽一张牌，
并且累加上面的值。 两个人都用最优策略， 求先手能够累积都最大值。

分析： 经典dp问题。 对于当前人来说， 决策的依据在于：取完某一头的牌（牌面指为a）， 剩下的局面让对手取走最优的情况之后， 可以可以取得剩下局面里面的牌面值为b， 其中a+b要最大
采用了记忆化的写法， 预处理一下sum

```
#include<cstdio>
#include<cstring>
#define maxab(a, b) ((a) > (b) ? (a) : (b))
using namespace std;
const int N = 201;
int a[N];
int dp[N][N], sum[N][N];

int solve(int s, int e){
    if(dp[s][e] != -1) return dp[s][e];
    if(s == e) return a[s];

    dp[s+1][e] = solve(s+1, e);
    dp[s][e-1] = solve(s, e-1);
    int s1 = a[s] + sum[s+1][e] - dp[s+1][e];
    int s2 = a[e] + sum[s][e-1] - dp[s][e-1];
    if(s1 > s2) return s1;
    return s2;
}

int main(){
    freopen("game1.in", "r", stdin);
    freopen("game1.out", "w", stdout);
    int n;
    scanf("%d", &n);
    for(int i = 0; i < n; ++i){
        scanf("%d", &a[i]);
        sum[i][i] = a[i];
    }
    for(int i = 0; i < n; ++i){
        for(int j = i+1; j < n; ++j){
            sum[i][j] = sum[i][j-1] + a[j];
        }
    }
    memset(dp, -1, sizeof(dp));
    int ans = solve(0, n-1);
    printf("%d %d\n", ans, sum[0][n-1] - ans);
    return 0;
}
```
</br>
