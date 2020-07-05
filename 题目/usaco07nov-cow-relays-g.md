# 题目

For their physical fitness program, $N (2 ≤ N ≤ 1,000,000)$ cows have decided to run a relay race using the $T (2 ≤ T ≤ 100)$ cow trails throughout the pasture.

Each trail connects two different intersections $(1 ≤ I1_i ≤ 1,000; 1 ≤ I2_i ≤ 1,000)$, each of which is the termination for at least two trails. The cows know the lengthi of each trail $(1 ≤ lengthi  ≤ 1,000)$, the two intersections the trail connects, and they know that no two intersections are directly connected by two different trails. The trails form a structure known mathematically as a graph.

To run the relay, the N cows position themselves at various intersections (some intersections might have more than one cow). They must position themselves properly so that they can hand off the baton cow-by-cow and end up at the proper finishing place.

Write a program to help position the cows. Find the shortest path that connects the starting intersection (S) and the ending intersection (E) and traverses exactly N cow trails.

给出一张无向连通图，求S到E经过k条边的最短路。

# 输入格式

* Line $1$: Four space-separated integers: N, T, S, and E

* 一行四个正整数 $N,T,S,E$ ，意义如题面所示。

* Lines $2..T+1$: Line $i+1$ describes trail i with three space-separated integers: $length_i$ , $I1_i$ , and $I2_i$


* 接下来 $T$ 行每行三个正整数 $w,u,v$，分别表示路径的长度，起点和终点。

# 输出格式

* Line 1: A single integer that is the shortest distance from intersection S to intersection E that traverses exactly N cow trails.

* 一行一个整数表示图中从 $S$ 到 $E$ 经过 $N 条边的最短路长度。

# 输入样例

```
2 6 6 4
11 4 6
4 4 8
8 4 9
6 6 8
2 6 9
3 8 9
```
# 输出样例

```
10
```

# 题解

[矩阵](https://oi-wiki.org/math/matrix/)我不熟,看了大佬的一个式子:

把经过$x$个点的最短路的邻接矩阵$X$和经过$y$个点的最短路的邻接矩阵$Y$合并的式子为:

$A_{i,j}=min(A_{i,j},X_{i,k}+Y_{k,j})$

把输入转成邻接矩阵后,这个邻接矩阵可以看作恰好经过一个点的最短路,然后转移$n-1$次就可以了

矩阵相乘时,需要使用[快速幂](https://oi-wiki.org/math/quick-pow/)优化

# 代码


```c++

#include <cstdio>
#include <cstring>
#define min(a, b) (a < b ? a : b)
int num[1000005], n, s, t, e, tol, x, y, z;
struct map {
    int data[500][500];
    map operator*(const map &other) const {
        map c;
        for (int k = 1; k <= tol; k++)
            for (int i = 1; i <= tol; i++)
                for (int j = 1; j <= tol; j++)
                    c.data[i][j] =
                        min(c.data[i][j], data[i][k] + other.data[k][j]);
        return c;
    }
    map() { memset(data, 0x3f3f3f3f, sizeof(data)); }
} dis, ans;
inline int input() { int t; scanf("%d", &t);  return t; }
int main() {
    n = input() - 1, t = input(), s = input(), e = input();
    for (int i = 1; i <= t; i++) {
        x=input();
        if(!num[y=input()])num[y] = ++tol;
        if(!num[z=input()])num[z] = ++tol;
        dis.data[num[y]][num[z]] = dis.data[num[z]][num[y]] = x;
    }
    ans = dis;
    while (n) (n & 1) && (ans = ans * dis, 0), dis = dis * dis, n >>= 1;
    printf("%d",ans.data[num[s]][num[e]]);
}
```
