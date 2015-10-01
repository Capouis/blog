---
layout: default
title: USACO-Chapter Three Solution
comments: true
---

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
