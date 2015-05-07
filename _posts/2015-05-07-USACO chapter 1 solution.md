---
layout: default
title: USACO - Chapter One Solution
comments: true
---

提交地址：  [USACO]("http://train.usaco.org/usacogate")

提交需要注意的地方：
* 每道题目均提交文件至USACO测试服务器， 每个提交文件开头格式为：

```
/*
ID: Your ID
LANG: The language U use
TASK: the problem's short name
*/
```

* 每道题目都是从文件输入输出， 所以需要在程序中将标准输入输出流重定向至输入输出文件， C++风格可参考我的代码， 其中的
输入输出数据的文件名题目中会给定， 注意看清题目。
<br/> 
----------------------------------------------------------------------------------------------------------------------------

* Your Ride Is Here

题意：给定两个字符串只包含大写英文字母， 计算两个字符串对应的编码mod 47 之后是否相等

分析：对于每个字符ch，值ch-'A'+1， 计算mod值的时候要用：(a*b) mod n  = (( a mod n ) * (b mod n)) mod n 来防止溢出

```
/*
ID: geek7774
LANG: C++
TASK: ride
*/
#include<cstdio>
#include<iostream>
#include<cstring>
using namespace std;
const int MOD = 47;

inline int foo(string s){
    int ret = 1;
    int len = s.length();
    for(int i = 0; i < len; ++i){
        ret *= (s[i]-'A'+1);
        ret %= MOD;
    }
    return ret;
}

int main(){
    freopen("ride.in", "r", stdin);
    freopen("ride.out", "w", stdout);

    string s1, s2;
    cin >> s1 >> s2;
    if(foo(s1) == foo(s2)){
        puts("GO");
    }
    else{
        puts("STAY");
    }
    return 0;
}
```
