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

* Healthy Holsteins

题意： 每头cow都需要一定的营养， 有v种营养， 每种营养必须到达vi的量。 现有n种能量源， 每种能量源可以提供v种营养， 现在需要求出最少需要几种能量源， 达到营养目标， 成为一头healthy cow.

分析： 考虑到数据范围， 可以用dfs搜索来搜索整个解空间。 当然在搜索到过程种要尽量剪枝

```
#include<cstdio>
#include<cstring>
#include<vector>
using namespace std;
const int N = 16;
typedef struct{
    int v[N<<1];
}contain;
contain types[N];

int need[N<<1];
int ans = 0, v, n;
//vector<int > root, *p;
int root[N];

void solve(int *b, int *c, int k, int num){
    if(num > ans) return ;

    bool flag = false;
    for(int i = 0; i < v; ++i){
        if(b[i] < need[i]){
            flag = true;
            break;
        }
    }
    if(!flag){
        if(num < ans){
            ans = num;
            for(int i = 0; i < num; ++i){
                root[i] = c[i];
            }
        }
        return ;
    }

    if(k == n) return;

    for(int i = 0; i < v; ++i){
        b[i] += types[k].v[i];
    }
    c[num] = k + 1;
    solve(b, c, k+1, num+1);

    for(int i = 0; i < v; ++i){
        b[i] -= types[k].v[i];
    }
    solve(b, c, k+1, num);
}

int main(){
    freopen("holstein.in", "r", stdin);
    freopen("holstein.out", "w", stdout);
    scanf("%d", &v);
    for(int i = 0; i < v; ++i){
        scanf("%d", &need[i]);
    }
    scanf("%d", &n);
    for(int i = 0; i < n; ++i){
        for(int j = 0; j < v; ++j){
            scanf("%d", &types[i].v[j]);
        }
    }

    int b[N<<1], c[N<<1];
    memset(b, 0, sizeof(b));
    ans = 1e9;
    solve(b, c, 0, 0);
    printf("%d", ans);
    for(int i = 0; i < ans; ++i){
        printf(" %d", root[i]);
    }
    printf("\n");
    return 0;
}
```
</br>


* Hamming codes

题意：给出N, B, D, 给出hamming code的定义： binary distance，也就是a,b分别写成二进制之后，每一位上如果数字不同也就是一个0，一个1算一个distance。 需要求出N个数， 每个数表示成B位二进制数之后， 两两之间的hamming code >= D

分析： 这里有一个很trick的想法： 0肯定可以作为N个数的第一个， 因为假设最小的数为k， 而不是0， 那么用k去xor这N个数，得到的新的序列依然是满足条件的（两个数xor同一个数， hamming code并不会改变）， 而k xor k = 0, 所以0肯定是第一个数。 确定第一个数， 就可以进行枚举判断， 直到找到N个数为止


```
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<vector>
using namespace std;

int N, B, D;
vector<int > ans;

bool check(int a, int b){
    int bit = a ^ b;
    int cnt = 0;
    for(int i = 0; i < B; ++i){
        if((1<<i) & bit){
            ++cnt;
            if(cnt >= D) return true;
        }
    }
    return false;
}

bool solve(int cnt, int k){
    for(int i = 0; i < cnt; ++i){
        if(!check(k, ans[i])) return false;
    }
    return true;
}

int main(){
    freopen("hamming.in", "r", stdin);
    freopen("hamming.out", "w", stdout);
    scanf("%d%d%d", &N, &B, &D);
    ans.clear();
    ans.push_back(0);

    int cnt = 1, cur = 1;
    while(cnt < N){
        if(solve(cnt, cur)){
            ans.push_back(cur);
            ++cnt;
        }
        ++cur;
    }

    for(int i = 0; i < ans.size(); ++i){
        if(i > 0 && i%10 == 0) printf("\n");
        else{
            if(i != 0){
                printf(" ");
            }
        }
        printf("%d", ans[i]);
    }
    printf("\n");
    return 0;
}
```
</br>


* Preface Numbering

题意: 这道题目是关于罗马编码的问题， 给出一些罗马编码的规则， 需要求出1～N的罗马数字表示里面各种罗马字母出现的次数。

分析: 刚开始觉得这道题目相当繁琐， 后来慢慢分析之后发现可以递归解决。 十位百位千位的情况与个位等同。 只要将个位的数字表达描述好， 就可以用来模拟高位的情况。

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;

char *roman[] = {
"", "I", "II", "III", "IV", "V",
"VI", "VII", "VIII", "IX", "X"
};

char* solve(int n, char *val){
    static char ans[10];
    char *p = ans;
    for(char *i = roman[n]; *i; ++p, ++i){
        if(*i == 'I'){
            *p = val[0];
        }
        else if(*i == 'V'){
            *p = val[1];
        }
        else if(*i == 'X'){
            *p = val[2];
        }
    }
    *p = '\0';
    return ans;
}

char* evlos(int n){
    char buffer[31];
    strcpy(buffer, "");
    strcat(buffer, solve(n/1000, "M"));
    strcat(buffer, solve(n/100%10, "CDM"));
    strcat(buffer, solve(n/10%10, "XLC"));
    strcat(buffer, solve(n%10, "IVX"));
//    printf("%s\n", buffer);
    return buffer;
}

int main(){
    freopen("preface.in", "r", stdin);
    freopen("preface.out", "w", stdout);
    int cnt[128];
    for(char *s = "IVXLCDM"; *s; ++s){
        cnt[*s] = 0;
    }

    int n;
    scanf("%d", &n);
    for(int i = 1; i <= n; ++i){
        for(char *r = evlos(i); *r; ++r){
            ++cnt[*r];
        }
    }

    for(char *s = "IVXLCDM"; *s; ++s){
        if(cnt[*s]){
            printf("%c %d\n", *s, cnt[*s]);
        }
    }
    return 0;
}
```
</br>

* Subset Sums

题意: 给出1~N这N个数， 要求把他们分成两份， 两份的sum要相等。

分析: 经典dp问题， 属于背包的范畴。 问题转化为： 从1-N中选k个数， 要求这k个数的和为s. s为1~N和值的一半。
算出的结果发现重复算了两次， 所以要除以2.

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int N = 40;
int dp[N];

int main(){
    freopen("subset.in", "r", stdin);
    freopen("subset.out", "w", stdout);
    int n;
    scanf("%d", &n);
    int s = (n)*(n+1)/2;
    if(s & 1){
        printf("\0");
    }
    else{
        s >>= 1;
        memset(dp, 0, sizeof(dp));
        dp[0] = 1;
        for(int i = 1; i <= n; ++i){
            for(int j = s; j >= i; --j){
                dp[j] = dp[j] + dp[j-i];
            }
        }
        printf("%d\n", dp[s]>>1);
    }
    return 0;
}
```
</br>


* Runaround Numbers

题意： 求满足s条件的数， s条件：一个数字从第一个数字开始， 按本位的数字往下循环访问， 要求能回到首位数字， 而且回到首位数字的时候， 所有的数字刚好被访问一次。  比如147， 刚开始1表示访问1之后的第一个数字4， 之后访问4之后的第4个数字， 到达7， 之后访问7之后的第7个数字，到达1， 至此回到第一个数字， 并且所有第数字被访问了一次， 所以147是满足条件第数。

分析：当第二次访问某个数字当时候， 说明肯定进入了某个循环节， 如果此时并非所有数字都访问过， 那么之后再也不可能被访问到。 同时有可能在数字的后面进入循环节， 再也回不到首位数字。 所以可以通过访问的数字次数进行控制。

```
#include<cstdio>
#include<cstring>
#include<cstdlib>
using namespace std;
char buf[10086];
bool vis[10];
bool digit[128];

bool solve(int n){
    sprintf(buf, "%d", n);
    memset(digit, false, sizeof(digit));
    for(char *p = buf; *p; ++p){
        if(*p == '0') return false;
        if(digit[*p] == true){
            return false;
        }
        digit[*p] = true;
    }
    memset(vis, false, sizeof(vis));
    int cnt = 1, len = strlen(buf), p = 0;
    vis[0] = true;
    if(len == 1) return true;

    while(true){
        p = (p + buf[p] - '0')%len;
        if(vis[p] == true){
            return false;
        }
        vis[p] = true, ++cnt;
        if(cnt == len){
            p = (p + buf[p] - '0')%len;
            if(p == 0){
                return true;
            }
            return false;
        }
    }
}

int main(){
    freopen("runround.in", "r", stdin);
    freopen("runround.out", "w", stdout);
    int n;
    scanf("%d", &n);
    while(++n){
        if(solve(n)){
            printf("%d\n", n);
            break;
        }
    }
    return 0;
}
```
</br>


* Party Lamps

题意：给出四种操作还有一些lamps, 现在需要执行c次操作， 要求所有的lamp的状态满足给定的条件。

分析： 这种类型的题目， 都要注意：几种操作先后顺序是无关的 & 一种操作执行多次是没有意义的， 等效于执行一次或者没有执行。 那么这样就可以枚举操作了， 因为要满足操作c次， 如果枚举的操作为k次， 只要c-k是偶数就ok了

```
#include<cstdio>
#include<cstring>
#include<vector>
#include<string>
#include<algorithm>
using namespace std;
const int N = 101;
bool isLight[N];
int on[N], off[N];
int n, c, on_cnt, off_cnt;
vector<string > sb;
bool cmp(string a, string b){
    return a < b;
}

void solve(){
    bool suc = false;
    sb.clear();
    //int types = (c <= 16 ? c : 16);
    for(int i = 0; i < 16; ++i){
        int s = i, bit1 = 0;
        while(s){
            s -= s & (-s);
            ++bit1;
        }
        if(bit1 > c) continue;
        if((c-bit1) & 1) continue;
        for(int i = 0; i < n; ++i){
            isLight[i] = 1;
        }
        if(i & (1<<0)){
            for(int j = 0; j < n; ++j){
                isLight[j] ^= 1;
            }
        }
        if(i & (1<<1)){
            for(int j = 0; j < n; j += 2){
                isLight[j] ^= 1;
            }
        }
        if(i & (1<<2)){
            for(int j = 1; j < n; j += 2){
                isLight[j] ^= 1;
            }
        }
        if(i & (1<<3)){
            for(int j = 0; j < n; j += 3){
                isLight[j] ^= 1;
            }
        }
        bool flag = true;
        for(int j = 0; j < on_cnt; ++j){
            if(!isLight[on[j]]){
                flag = false;
                break;
            }
        }
        for(int j = 0; j < off_cnt; ++j){
            if(isLight[off[j]]){
                flag = false;
                break;
            }
        }
        if(flag){
            suc = true;
            string tmp = "";
            for(int j = 0; j < n; ++j){
                tmp += isLight[j] + '0';
            }
            //tmp += '\0';
            sb.push_back(tmp);
        }
    }
    if(!suc){
        printf("IMPOSSIBLE\n");
        return ;
    }

    sort(sb.begin(), sb.end(), cmp);
    for(int i = 0; i < sb.size(); ++i){
        printf("%s\n", sb[i].c_str());
    }
}

int main(){
    freopen("lamps.in", "r", stdin);
    freopen("lamps.out", "w", stdout);
    scanf("%d%d", &n, &c);
    on_cnt = 0, off_cnt = 0;
    int val;
    while(~scanf("%d", &val)){
        if(val == -1) break;
        on[on_cnt++] = val - 1;
    }
    while(~scanf("%d", &val)){
        if(val == -1) break;
        off[off_cnt++] = val - 1;
    }
    solve();
    return 0;
}
```
</br>
