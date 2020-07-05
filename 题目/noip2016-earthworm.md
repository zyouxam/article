# 题目

本题中，我们将用符号 $\lfloor c \rfloor$ 表示对 $c$ 向下取整，例如：$\lfloor 3.0 \rfloor = \lfloor 3.1 \rfloor = \lfloor 3.9 \rfloor = 3$。

蛐蛐国最近蚯蚓成灾了！隔壁跳蚤国的跳蚤也拿蚯蚓们没办法，蛐蛐国王只好去请神刀手来帮他们消灭蚯蚓。

蛐蛐国里现在共有 $n$ 只蚯蚓（$n$ 为正整数）。每只蚯蚓拥有长度，我们设第 $i$ 只蚯蚓的长度为 $a_i(i=1,2,\dots,n)$，并保证所有的长度都是非负整数（即：可能存在长度为 $0$ 的蚯蚓）。

每一秒，神刀手会在所有的蚯蚓中，准确地找到最长的那一只（如有多个则任选一个）将其切成两半。神刀手切开蚯蚓的位置由常数 $p$（是满足 $0 < p < 1$的有理数）决定，设这只蚯蚓长度为 $x$，神刀手会将其切成两只长度分别为 $\lfloor px \rfloor$ 和 $x - \lfloor px \rfloor$ 的蚯蚓。特殊地，如果这两个数的其中一个等于 $0$，则这个长度为 $0$ 的蚯蚓也会被保留。此外，除了刚刚产生的两只新蚯蚓，其余蚯蚓的长度都会增加 $q$（是一个非负整常数）。

蛐蛐国王知道这样不是长久之计，因为蚯蚓不仅会越来越多，还会越来越长。蛐蛐国王决定求助于一位有着洪荒之力的神秘人物，但是救兵还需要 $m$ 秒才能到来……（$m$ 为非负整数）

蛐蛐国王希望知道这 $m$ 秒内的战况。具体来说，他希望知道：

$m$ 秒内，每一秒被切断的蚯蚓被切断前的长度（有 $m$ 个数）；
$m$ 秒后，所有蚯蚓的长度（有 $n + m$ 个数）。
蛐蛐国王当然知道怎么做啦！但是他想考考你……

# 输入格式

输入格式
第一行包含六个整数 $n,m,q,u,v,tn,m,q,u,v,t$，其中：$n,m,q$ 的意义见【问题描述】；$u,v,t$ 均为正整数；你需要自己计算 $p=\frac u v$（保证 $0 < u < v$）；$t$ 是输出参数，其含义将会在【输出格式】中解释。

第二行包含 $n$ 个非负整数，为 $a_1, a_2, \dots$ ，即初始时 $n$ 只蚯蚓的长度。

同一行中相邻的两个数之间，恰好用一个空格隔开。

保证$ 1 \leq n \le 10^5,0 \leq m \leq 7 \times 10 ^ 6,0 < u < v < 10^9，0<=q<=200,1< t <71,0< a_i <10^8$

# 输出格式

第一行输出 $\left \lfloor \frac{m}{t} \right \rfloor$个整数，按时间顺序，依次输出第 $t$ 秒，第 $2t$ 秒，第 $3t$ 秒,……被切断蚯蚓（在被切断前）的长度。

第二行输出 $\left \lfloor \frac{n+m}{t} \right \rfloor$个整数，输出 $m$ 秒后蚯蚓的长度；需要按从大到小的顺序，依次输出排名第 $t$，第 $2t$，第 $3t$，……的长度。

同一行中相邻的两个数之间，恰好用一个空格隔开。即使某一行没有任何数需要输出，你也应输出一个空行。

请阅读样例来更好地理解这个格式。

# 输入输出样例

## 输入1
```cpp
3 7 1 1 3 1
3 3 2
```
## 输出1
```cpp
3 4 4 4 5 5 6
6 6 6 5 5 4 4 3 2 2
```
## 输入2
```cpp
3 7 1 1 3 2
3 3 2
```
## 输出2
```cpp
4 4 5
6 5 4 3 2
```
## 输入3
```cpp
3 7 1 1 3 9
3 3 2
```
## 输出3
```cpp
//空行
2
```

# 题解

假设其它蚯蚓不会边长,这道题直接模拟的话,就是要做到查询最大值,删除最大值,插入新的值三种操作,使用优先队列即可实现.

但蚯蚓会变长,不过也只是需要改一下,因为除了最大值会被拆成两个数,其他数都会增加$q$,设最大值为$x$,那么只需要认为$x$被拆成$\lfloor px \rfloor - q$和$x - \lfloor px \rfloor - q$ ,然后把所有数都加上$q$,这样是等价的,.

注意这里"把所有数都加上$q$"是延迟操作,不是当时就加上,而是把每次操作加上的$q$加起来,设为$delta$,因为所有数都是同时加上$q$,所以只需要一个 $delta$,每次加$q$,$delta$就自增$q$,然后在读取集合中的数字时,先加上$delta$再使用即可.

所以对于每一秒:

1. 取出集合中的最大值$x$,令$x=x+delta$
2. 把$\lfloor px \rfloor - q-delta$和$x - \lfloor px \rfloor - q-delta$插入集合
3. 令 $delta=delta+q$

这三步重复$m$次,集合中的数就是最终的数字,但是问题来了,$0 \leq m \leq 7 \times 10 ^ 6$,这个复杂度太高,还是会t,所以得优化.

题中给了$0 < p < 1$,设 $x_1,x_2$ 为非负整数

当$x_1 \ge x_2$

$\because \lfloor px_1 + q \rfloor \ge \lfloor px_2 + pq \rfloor$

$\therefore \lfloor px_1  \rfloor + q \ge \lfloor p(x_2 + q) \rfloor$

$\because x_1 - x_2 \ge p(x_1-x_2)$

$\therefore x_1-px_1 \ge x_2-px_2 \ge x_2 - p(x_2+q)$

$\therefore \lfloor x_1 - px_1 \rfloor +q\ge \lfloor x_2-p(x_2+q)\rfloor+q$

$\therefore x_1-\lfloor px_1\rfloor +q \ge x_2+q-\lfloor p(x_2)+q \rfloor$

得到这个有什么用呢?如果$x_1$在$x_2$之前被取出集合,在一秒后,$x_1$分成的两个数$\lfloor px_1  \rfloor + q$和$x-\lfloor px_1  \rfloor + q$ **分别** 不小于$x_2$分成的两个数$\lfloor p(x_2 + q) \rfloor$和$x_2+q-\lfloor p(x_2)+q \rfloor$,所以新产生的两个数也**分别**单调递减.

由于**分别**单调递减,所以只需要分两个队列保存即可,比如分成的前半部分一个队列,后半部分一个队列,再和原本的队列的首项比较最大值,最大的取出来分.

到这里,基本思路就明确了,但是当我高兴地写完,往[洛谷](https://www.luogu.com.cn/problem/P2827)上面交也过了,当我往Vjudge上交的时候,却TLE了

这是我当时的代码:

```cpp
#include <cstdio>
#include <queue>
using namespace std;
long long INF = 0x3f3f3f3f3f3f3f3f;
int n, m, q, u, v, t, delta = 0, w;
priority_queue<long long> pq;
queue<long long> q1, q2;
inline long long input() { long long t; scanf("%lld", &t); return t; }
int main() {
    scanf("%d%d%d%d%d%d", &n, &m, &q, &u, &v, &t);
    for (int i = 1; i <= n; i++) pq.push(input());
    for (int i = 1; i <= m; i++) {
        long long maxx = -INF;
        if (pq.size() && maxx < pq.top()) maxx = pq.top(), w = 0;
        if (q1.size() && maxx < q1.front()) maxx = q1.front(), w = 1;
        if (q2.size() && maxx < q2.front()) maxx = q2.front(), w = 2;
        w == 1 ? q1.pop() : w == 2 ? q2.pop() : pq.pop();
        maxx += delta;
        q1.push(maxx * u / v - delta - q), q2.push(maxx - maxx * u / v - delta - q);
        delta += q;
        if (i % t == 0) printf("%lld ", maxx);
    }
    printf("\n");
    for (int i = 1; i <= n + m; i++) {
        long long maxx = -INF;
        if (pq.size() && maxx < pq.top()) maxx = pq.top(), w = 0;
        if (q1.size() && maxx < q1.front()) maxx = q1.front(), w = 1;
        if (q2.size() && maxx < q2.front()) maxx = q2.front(), w = 2;
        w == 1 ? q1.pop() : w == 2 ? q2.pop() : pq.pop();
        if (i % t == 0) printf("%lld ", maxx + delta);
    }
    return 0;
}
```

这就比较神奇,我看了别人的代码,发现不少人的输入用数组输入然后排序,虽然理论上和我这个复杂度一样,但众所周知,STL自带大常数,所以我把优先队列删了,改成了这样:

```cpp
#include <algorithm>
#include <cstdio>
#include <queue>
using namespace std;
long long INF = 0x3f3f3f3f3f3f3f3f;
int n, m, q, u, v, t, delta = 0, w, a[100005], ai = 1;
queue<long long> q1, q2;
bool cmp(int a, int b) { return a > b; }
int main() {
    scanf("%d%d%d%d%d%d", &n, &m, &q, &u, &v, &t);
    for (int i = 1; i <= n; i++) scanf("%lld", &a[i]);
    sort(a + 1, a + n + 1, cmp);
    for (int i = 1; i <= m; i++) {
        long long maxx = -INF;
        if (ai <= n && maxx < a[ai]) maxx = a[ai], w = 0;
        if (q1.size() && maxx < q1.front()) maxx = q1.front(), w = 1;
        if (q2.size() && maxx < q2.front()) maxx = q2.front(), w = 2;
        if(w == 1) q1.pop();
        else if (w == 2) q2.pop();
        else ai++;
        maxx += delta;
        q1.push(maxx * u / v - delta - q), q2.push(maxx - maxx * u / v - delta - q);
        delta += q;
        if (i % t == 0) printf("%lld ", maxx);
    }
    printf("\n");
    for (int i = 1; i <= n + m; i++) {
        long long maxx = -INF;
        if (ai <= n && maxx < a[ai]) maxx = a[ai], w = 0;
        if (q1.size() && maxx < q1.front()) maxx = q1.front(), w = 1;
        if (q2.size() && maxx < q2.front()) maxx = q2.front(), w = 2;
        if(w == 1) q1.pop();
        else if (w == 2) q2.pop();
        else ai++;
        if (i % t == 0) printf("%lld ", maxx + delta);
    }
    return 0;
}
```

可是悲伤的事情来了,居然还是TLE...,这次是同学提醒我,队列用不上`long long`,只需要计算的时候用`long long`就可以,因为**使用long long的queue比使用int的queue要慢**,这点看似不起眼的优化却让我AC了,这是我第一次被STL坑的这么惨,以后长教训了,能不用STL就不用

# 代码

```cpp

#include <algorithm>
#include <cstdio>
#include <queue>
using namespace std;
long long INF = 0x3f3f3f3f3f3f3f3f;
int n, m, q, u, v, t, delta = 0, w, a[100005], ai = 1;
queue<int> q1, q2;
bool cmp(int a, int b) { return a > b; }
int main() {
    scanf("%d%d%d%d%d%d", &n, &m, &q, &u, &v, &t);
    for (int i = 1; i <= n; i++) scanf("%lld", &a[i]);
    sort(a + 1, a + n + 1, cmp);
    for (int i = 1; i <= m; i++) {
        long long maxx = -INF;
        if (ai <= n && maxx < a[ai]) maxx = a[ai], w = 0;
        if (q1.size() && maxx < q1.front()) maxx = (long long)q1.front(), w = 1;
        if (q2.size() && maxx < q2.front()) maxx = (long long)q2.front(), w = 2;
        if(w == 1) q1.pop();
        else if (w == 2) q2.pop();
        else ai++;
        maxx += delta;
        q1.push(maxx * u / v - delta - q), q2.push(maxx - maxx * u / v - delta - q);
        delta += q;
        if (i % t == 0) printf("%lld ", maxx);
    }
    printf("\n");
    for (int i = 1; i <= n + m; i++) {
        long long maxx = -INF;
        if (ai <= n && maxx < a[ai]) maxx = a[ai], w = 0;
        if (q1.size() && maxx < q1.front()) maxx = (long long)q1.front(), w = 1;
        if (q2.size() && maxx < q2.front()) maxx = (long long)q2.front(), w = 2;
        if(w == 1) q1.pop();
        else if (w == 2) q2.pop();
        else ai++;
        if (i % t == 0) printf("%lld ", maxx + delta);
    }
    return 0;
}
```