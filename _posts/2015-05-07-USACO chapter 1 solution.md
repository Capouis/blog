---
layout: default
title: USACO - Chapter One Solution
comments: true
---
 
提交地址： [USACO](http://train.usaco.org/usacogate)
考虑到USACO经常挂掉，  可以在其他的地方进行测试， 比如：[USACO-Copy](http://cqoi.net:2012/problemset.php)

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
<br/>

* Greedy Gift Givers

题意：每个人都准备一些钱平均分给其他人，不能分的部分留给自己，计算每个人的得失。 

分析：用一个map来维护每一个人当前得失。

```
/*
ID: geek7774
LANG: C++
TASK: ride
*/
#include<cstdio>
#include<iostream>
#include<map>
#include<cstring>
using namespace std;
map<string, int> mp;
string np[11];
char name[20];
string _name;
int n;

int main(){
    freopen("gift1.in", "r", stdin);
    freopen("gift1.out", "w", stdout);

    scanf("%d", &n);
    for(int i = 0; i < n; ++i){
        scanf("%s", name);
        np[i] = name;
    }

    mp.clear();
    int bouns, p;
    for(int i = 0; i < n; ++i){
        scanf("%s", name);
        scanf("%d %d", &bouns, &p);
        _name = name;
        int g, l;
        if(p == 0){
            g = 0, l = bouns;
        }
        else{
            g = bouns/p, l = bouns - g*p;
        }
        if(mp.find(_name) == mp.end()){
            mp[_name] = l - bouns;
        }
        else{
            mp[_name] += l - bouns;
        }

        for(int j = 0; j < p; ++j){
            scanf("%s", name);
            _name = name;
             if(mp.find(_name) == mp.end()){
                mp[_name] = g;
            }
            else{
                mp[_name] += g;
            }
        }
    }
    for(int i = 0; i < n; ++i){
        printf("%s %d\n", np[i].c_str(), mp[np[i]]);
    }
    return 0;
}
```
<br/>

* Friday the Thirteenth

题意： 已经1990年1月1号是星期一，给出一个n， 推算出1900 - 1900+n-1这n年中每个月的13号分布在星期一 ~ 星期天的各个天数

分析： 首先考虑闰年， 其次可先推算出1900年1月13号是星期六， 下面就开始进行模拟， 每隔七天循环一个星期 ，统计每个13号对应的星期几。

```
/*
ID: geek7774
LANG: C++
TASK: 
*/
#include<cstdio>
#include<cstring>
using namespace std;
int month[2][12]= {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31, 31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
int ans[8];

bool judge(int y){
    if(y%100 != 0 && y%4 == 0){
        return 1;
    }
    if(y%400 == 0){
        return 1;
    }
    return 0;
}

int main(){
    int n;
    scanf("%d", &n);
    int s = 5;
    memset(ans, 0, sizeof(ans));
    for(int i = 0; i < n; ++i){
        int y = judge(1900+i);
        for(int j = 0; j < 12; ++j){
            ans[s]++;
            s = (s + month[y][j]%7) % 7;
        }
    }
    for(int i = 0; i < 6; ++i){
        printf("%d ", ans[(i+5)%7]);
    }
    printf("%d\n", ans[4]);
    return 0;
}
```
<br/>

* Broken Necklace

题意： 有一个循环的项链， 有R G W三种颜色的珠子,  现在可以从某一处断开， 然后从断开点往两边同时收集， 能够连续收集的条件是：珠子的颜色要保持一致， 其中W既可以当做是R也可以当做G来收集， 问最多能收集多少珠子。

分析： 模拟题， 枚举断开点， 记录收集过程中遇到的颜色， 颜色的分析是整个解题的关键：

* 遇到W判断无论如何都能收集，而且对当前收集的颜色确定没有影响
* 遇到R或者G， 首先要check当前收集的颜色有没有确定， 如果确定了要必须保持一致才能收集， 如果不确定则可以收集， 而且也可以确定当前的收集的颜色

当然， 本地可以先处理出从某一点往两边可以收集的R或者G的珠子的最大个数， 因为满足DP的条件， 因此可以用DP解决

```
/*
ID: geek7774
LANG: C++
TASK: 
*/
#include<cstdio>
#include<cstring>
using namespace std;
const int N = 360;
char s[N];
int n;

int foo(int idx){
    int l = idx, r = idx+1;
    int ret = 0;
    char col = s[l];
    while(l != r){
        if(col == 'w' || s[l] == col || s[l] == 'w'){
            if(s[l] != 'w') col = s[l];
            l = (l-1+n) % n;
            ++ret;
        }
        else{
            break;
        }
    }

    l = (l+1+n) % n;
    col = s[r];
    while(l != r){
        if(col == 'w' || s[r] == col || s[r] == 'w'){
            if(s[r] != 'w') col = s[r];
            r = (r+1+n) % n;
            ++ret;
        }
        else{
            break;
        }
    }
    return ret;
}

int main(){
    scanf("%d", &n);
    scanf("%s", s);
    int ans = 0;
    for(int i = 0; i < n-1; ++i){
        int ret = foo(i);
        if(ret > ans){
            ans = ret;
        }
    }
    printf("%d\n", ans);
    return 0;
}
```
<br/>

* Milking Cows

题意：给出一些挤奶的时间段， 询问最长的连续工作时间， 以及最长的中断时间。

分析： 可以用贪心解决， 先将所有的时间区间按开始时间排序， 用s, e 来表示当前枚举的区间里面最左跟最右的范围， 一个个枚举下去， 判断i段开始时间a[i].l与当前最后的结束时间e的关系：

* a[i].l > e, 说明当前区间与之前的没有任何关系， 可以重新考虑， 于是更新当前最新的开始与结束的时间， 同时这种情况下，可能会影响最长的中断时间， 所以进行更新
* a[i].l < e, 说明当前区间与之前的最值有联系，判断a[i].e 与e的关系， 如果a[i].e <= e， 则直接忽略， 反之考虑这种情况下， 最长工作时间会不会有更新， 同时需要更新e

```
/*
ID: geek7774
LANG: C++
TASK: 
*/
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int N = 5001;

typedef struct node{
    int l, r;
    bool operator< (const node& s) const{
        if(l != s.l) return l < s.l;
        return r < s.r;
    }
}node;
node a[N];

int main(){
    int n;
    scanf("%d", &n);

    for(int i = 0; i < n; ++i){
        scanf("%d%d", &a[i].l, &a[i].r);
    }
    sort(a, a+n);

    int v1 = a[0].r - a[0].l, v2 = 0;
    int s = a[0].l, e = a[0].r;
    for(int i = 1; i < n; ++i){
        if(a[i].l > e){
            if(a[i].l - e > v2){
                v2 = a[i].l - e;
            }
            s = a[i].l, e = a[i].r;
        }
        else{
            if(a[i].r > e){
                if(a[i].r - s > v1){
                    v1 = a[i].r - s;
                }
                e = a[i].r;
            }
        }
    }
    printf("%d %d\n", v1, v2);
    return 0;
}
```
<br/>

* Transformations

题意： 给出几种矩阵的转换方式， 包括选择，对称等， 给出A,B两个矩阵， 给出A->B可以由哪种转换得到， 要求编号最小

分析： 分析A, B不同位置的数据是否满足特定的转换， 如果满足标记一下， 只有当所有的位置关系都能满足某种交换时， 这种交换才是有意义的

```
/*
ID: geek7774
LANG: C++
TASK:
*/
#include<cstdio>
#include<cstring>
char a[11][11], b[11][11];
int n, ans[10];

void trans() {
  memset(ans, 0, sizeof(ans));
  for(int i = 1; i <= n; i++)
    for(int j = 1; j <= n; j++){
      if(a[i][j] == b[j][n-i+1]) ans[1]++;
      if(a[i][j] == b[n-i+1][n-j+1]) ans[2]++;
      if(a[i][j] == b[n-j+1][i]) ans[3]++;
      if(a[i][j] == b[i][n-j+1]) ans[4]++;
      if(a[i][j] == b[i][j]) ans[6]++;
      if(a[i][n-j+1] == b[j][n-i+1]) ans[5]++;
      if(a[i][n-j+1] == b[n-i+1][n-j+1]) ans[7]++;
      if(a[i][n-j+1] == b[n-j+1][i]) ans[8]++;
    }

    for(int i = 1; i <= 8; i++){
        if(ans[i] == n*n){
            if(i < 7) printf("%d\n", i);
            else printf("5\n");
            return ;
        }
    }
    printf("7\n");
}

int main(){
    scanf("%d", &n);
    for(int i = 1; i <= n; i++)
       scanf("%s", a[i]+1);

    for(int i = 1; i <= n; i++)
       scanf("%s", b[i]+1);

    trans();
    return 0;
}
```
<br/>

* Name That Number

题意： 给定一个英文词典， 已知每个数字对应一些字母， 现在给出一个数字串， 问可以映射出字典里面的哪些字符串

分析： 在Nocow上面有一些类似hash这样的更好的做法， 但是考虑到这题的数据规模， 完全可以直接模拟解决

```
/*
ID: geek7774
LANG: C++
TASK:
*/
#include<cstdio>
#include<cstring>
#include<iostream>
using namespace std;
const char code[26]={'2','2','2','3','3','3','4','4','4',
'5','5','5','6','6','6','7','0','7','7','8','8','8','9','9','9','0'};
char a[13],b[13];

int main()
{
    freopen("namenum.in","r",stdin);
    freopen("namenum.out","w",stdout);
    freopen("dict.txt","r",stderr);
    bool flag = 1;
    cin >> a;

    while (fscanf(stderr,"%s",b) != EOF){
        bool sgn = 1;
        if (strlen(a) != strlen(b)) continue;
        for(int i = 0; b[i] && sgn; i++) sgn = sgn && (code[b[i]-'A']==a[i]);
        if (sgn) flag = 0, cout << b <<endl;
    }
    if (flag) cout << "NONE" << endl;
    return 0;
}
```
<br/>

* Palindromic Squares

题意： 给出一个b代表进制数， 要求1-300之间平方数满足回文关系的数

分析： 模拟题， 注意输出的时候是需要输出b进制下的数

```
/*
ID: geek7774
LANG: C++
TASK:
*/
#include<cstdio>
#include<cstring>
#include<iostream>
using namespace std;
const int N = 1001;
int a[N], b[N];

int main(){
    int bin;
    scanf("%d", &bin);
    for(int i = 1; i <= 300; ++i){
        int j, k = i*i, cnt;
        j = k;
        cnt = 0;
        while(j){
            a[cnt++] = j%bin;
            j /= bin;
        }

        bool suc = 1;
        for(int j = 0; j < cnt/2; ++j){
            if(a[j] != a[cnt-1-j]){
                suc = 0;
                break;
            }
        }
        if(suc){
            int cnt_ = 0, v = i;
            while(v){
                b[cnt_++] = v%bin;
                v /= bin;
            }
            for(int j = cnt_-1; j >= 0; --j){
                if(b[j] < 10){
                    printf("%d", b[j]);
                }
                else{
                    printf("%c", 'A'+b[j]-10);
                }
            }
            printf(" ");
            for(int j = cnt-1; j >= 0; --j){
                if(a[j] < 10){
                    printf("%d", a[j]);
                }
                else{
                    printf("%c", 'A'+a[j]-10);
                }
            }
            puts("");
        }
    }
    return 0;
}
```
<br/>
