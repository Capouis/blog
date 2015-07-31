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
    freopen("namenum.in", "r", stdin);
    freopen("namenum.out", "w", stdout);
    freopen("dict.txt", "r", stderr);
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

* Dual Palindromes

题意： 给定N, S， 求出N个大于S的整数， 满足： 每个整数在由2-10为基数表示的进制数中， 至少有两个是回文数

分析： 枚举， 判断每个基数表示下是否为回文数即可

```
/*
ID: geek7774
LANG: C++
TASK: dualpal
*/
#include<cstdio>
#include<cstring>
#define submit
using namespace std;
const int N = 33;
int a[N], ans[N];

int main(){
    #ifdef submit
    freopen("dualpal.in", "r", stdin);
    freopen("dualpal.out", "w", stdout);
    #endif

    int n, s;
    scanf("%d%d", &n, &s);
    int cnt = 0;
    for(int i = s + 1; cnt < n; ++i){
        int idx, j, k, p = 0;
        for(idx = 2; idx <= 10 && p < 2; ++idx){
            j = 0, k = i;
            while(k){
                a[j++] = k%idx;
                k /= idx;
            }
            int l;
            for(l = 0; l < j/2; ++l){
                if(a[l] != a[j-1-l]){
                    break;
                }
            }
            if(l == (j/2)){
                ++p;
            }
        }
        if(p == 2){
            ans[cnt++] = i;
        }
    }

    for(int i = 0; i < n; ++i){
        printf("%d\n", ans[i]);
    }
    return 0;
}
```
<br/>

* Mixing Milk

题意： 需要N加仑牛奶， 有M个farmer， 每个人可以提供每加仑a元的牛奶b加仑， 求买N加仑的牛奶最少需要多少钱

分析： 贪心一下， 按牛奶的价格排序， 先尽量买便宜的

```
/*
ID: geek7774
LANG: C++
TASK: milk
*/
#include<cstdio>
#include<cstring>
#include<algorithm>
#define submit
using namespace std;
const int N_ = 2e6 + 10;

typedef struct node{
    int cost, amount;

    bool operator < (const node & r) const{
        return cost < r.cost;
    }
}node;

node a[N_];

int main(){
    #ifdef submit
    freopen("milk.in", "r", stdin);
    freopen("milk.out", "w", stdout);
    #endif

    int N, M;
    scanf("%d%d", &N, &M);
    for(int i = 0; i < M; ++i){
        scanf("%d%d", &a[i].cost, &a[i].amount);
    }

    sort(a, a+M);

    int ans = 0;
    for(int i = 0; i < M; ++i){
        if(a[i].amount < N){
            ans += a[i].cost*a[i].amount;
            N -= a[i].amount;
        }
        else{
            ans += a[i].cost*N;
            break;
        }
    }
    printf("%d\n", ans);
    return 0;
}
```
<br/>

* Prime Cryptarithm

题意: 给出一个数字集合, 要求构造一个乘式, 3*2的格式, 乘法的中间数的位数也是有要求的, 类似:

```
      2 2 2
    x   2 2
     ------
      4 4 4
    4 4 4
  ---------
    4 8 8 4
 ```
 
要求乘式中的每个数字都在给出的数字集合中. 要求满足条件的等式的个数.

分析: 阿拉伯数字个数肯定不会超过10, 而且乘数跟被乘数的位数很小, 所以可以直接枚举. 枚举之后, 判断乘法过程中每个数的数字以及位数是否满足要求.


```
/*
ID: geek7774
LANG: C++
TASK: crypt1
*/
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
int a[11];
bool vis[11];

int main(){
    freopen("crypt1.in", "r", stdin);
    freopen("crypt1.out", "w", stdout);
    int n;
    scanf("%d", &n);
    memset(vis, 0, sizeof(vis));
    for(int i = 0; i < n; ++i){
        scanf("%d", &a[i]);
        vis[a[i]] = 1;
    }

    int ans = 0;
    for(int i = 0; i < n; ++i){
        if(a[i] == 0) continue;
        for(int j = 0; j < n; ++j){
            for(int k = 0; k < n; ++k){
                for(int m = 0; m < n; ++m){
                    if(a[m] == 0) continue;
                    for(int l = 0; l < n; ++l){
                        int x1 = 100*a[i] + 10*a[j] + a[k];
                        int tmp1 = x1*a[m], tmp2 = x1*a[l];
                        if(tmp1 < 100 || tmp1 >= 1000) continue;
                        if(tmp2 < 100 || tmp2 >= 1000) continue;

                        int res = tmp1*10+tmp2;
                        if(res < 1000 || res >= 10000) continue;
                        bool flag = true;
                        while(tmp1){
                            int  x2 = tmp1%10;
                            if(!vis[x2]){
                                flag = false;
                                break;
                            }
                            tmp1 /= 10;
                        }

                        while(tmp2){
                            int x2 = tmp2%10;
                            if(!vis[x2]){
                                flag = false;
                                break;
                            }
                            tmp2 /= 10;
                        }

                        while(res){
                            int x2 = res%10;
                            if(!vis[x2]){
                                flag = false;
                                break;
                            }
                            res /= 10;
                        }
                        if(flag){
                        //    printf("%d %d %d %d %d %d\n", x1, a[m], a[l], tmp1, tmp2, res);
                            ++ans;
                        }
                    }
                }
            }
        }

    }
    printf("%d\n", ans);
    return 0;
}
```
</br>

* Combination Lock

题意: 给出一个数字N, 代表一个数字环1-N, 且N跟1也是连接的(环). 给出两个三元组R: (a,b,c) S: (d,e,f), 要求构造三元组 T: (x,y,z) 满足dist(T, R)或者dist(S, T) <= 2, 这里的dist定义为数字距离, 比如(1,2,3)跟(4,5,6)的dist为: 4-1 + 5-2 + 6-3 = 9, 注意环的存在,比如N=10, 那么1跟10的距离只有1. 期望求出这样的三元组的个数.

分析: 题目中的N范围比较小(1 <= N <= 100), 而且dist要求的距离很小,不超过2, 所以brute枚举完全没有问题. 但是枚举过程中可能会有重复, 现在就是要判重. 可以考虑暴力开个vis数组或者hash一下. 我选择了hash的做法.

```
/*
ID: geek7774
LANG: C++
TASK: combo
*/
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
int n, a[3], b[3], c[3];
const int N = 101*100*100 + 101*100 + 101;
bool hash[N];
int ans = 0;

void dfs(int k, int *r){
    if(k == 3){
        int tmp = c[0]*100*100 + c[1]*100 + c[2];
        if(hash[tmp] == 0){
            ++ans;
            hash[tmp] = 1;
           // printf("%d %d %d\n", c[0], c[1], c[2]);
        }
        return;
    }

    for(int i = -2; i <= 2; ++i){
        c[k] = (r[k] + i + n) % n + 1;
        dfs(k+1, r);
    }
}

int main(){
    freopen("combo.in", "r", stdin);
    freopen("combo.out", "w", stdout);
    scanf("%d", &n);


    for(int i = 0; i < 3; ++i){
        scanf("%d", &a[i]);
    }
    for(int i = 0; i < 3; ++i){
        scanf("%d", &b[i]);
    }

    dfs(0, a);
    dfs(0, b);
    printf("%d\n" ,ans);
    return 0;
}
```
</br>
