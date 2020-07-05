# 题目

On another floor of the A.R.C. Markland-N, the young man Simon "Xenon" Jackson, takes a break after finishing his project early (as always). Having a lot of free time, he decides to put on his legendary hacker "X" instinct and fight against the gangs of the cyber world.

His target is a network of $n$ small gangs. This network contains exactly $n−1$ direct links, each of them connecting two gangs together. The links are placed in such a way that every pair of gangs is connected through a sequence of direct links.

By mining data, Xenon figured out that the gangs used a form of cross-encryption to avoid being busted: every link was assigned an integer from $0$ to $n−2$ such that all assigned integers are distinct and every integer was assigned to some link. If an intruder tries to access the encrypted data, they will have to surpass $S$ password layers, with $S$ being defined by the following formula:

$$s=\sum_{1\le u \le v \le n} mex(u,v)$$

Here, $mex(u,v)$ denotes the smallest non-negative integer that does not appear on any link on the unique simple path from gang $u$ to gang $v$.

Xenon doesn't know the way the integers are assigned, but it's not a problem. He decides to let his AI's instances try all the passwords on his behalf, but before that, he needs to know the maximum possible value of $S$, so that the AIs can be deployed efficiently.

Now, Xenon is out to write the AI scripts, and he is expected to finish them in two hours. Can you find the maximum possible $S$ before he returns?

# 输入格式

The first line contains an integer $n (2 \le n \le 3000)$, the number of gangs in the network.

Each of the next n−1 lines contains integers $u_i$ and $v_i (1 \le u_i,v_i \le n; u_i \ne v_i)$, indicating there's a direct link between gangs $u_i$ and $v_i$.

It's guaranteed that links are placed in such a way that each pair of gangs will be connected by exactly one simple path.

# 输出格式

print the maximum possible value of $S$ — the number of password layers in the gangs' network.

# 样例输入1

```cpp
3
1 2
2 3
```

# 样例输出1

````cpp
3
````

# 样例输入1
```cpp
5
1 2
1 3
1 4
3 5
```
# 样例输出1

```cpp
10
```

# 注意

In the first example, one can achieve the maximum $S$ with the following assignment:

![](https://img2020.cnblogs.com/blog/1975074/202004/1975074-20200411124308895-453452417.jpg)



With this assignment, $mex(1,2)=0$, $mex(1,3)=24$ and $mex(2,3)=1$. Therefore, $S=0+2+1=3$.

In the second example, one can achieve the maximum $S$ with the following assignment:

![](https://img2020.cnblogs.com/blog/1975074/202004/1975074-20200411124313438-387250702.jpg)



With this assignment, all non-zero mex value are listed below:

- $mex(1,3)=1$
- $mex(1,5)=2$
- $mex(2,3)=1$
- $mex(2,5)=2$
- $mex(3,4)=1$
- $mex(4,5)=3$

Therefore, $S=1+2+1+2+1+3=10$.

# 题解

![](2.jpg)

看一下样例2

首先考虑边权为$0$的这条边,只要通过这条边的,最小的整数就是$1$了,那么经过这条边路径的个数就是$0$贡献的代价,即这条边右边的点数量乘左边点数量:$2 \times 3 = 6$

再考虑$1$,如果单独考虑它,经过它的最小整数是$0$,对答案没有一点贡献了,所以必须和1组合起来,那么把$0-1$看做一个整体,右边一个点,左边3个点,所以贡献就是$1 \times 3 = 3$

注意这里的贡献是1的原因是之前已经有一层1的贡献,这里是2的贡献,所以每条链只多了1的贡献

对于$2$,必须和$0,1$组合起来,而且只能考虑$2-0-1$这一条链,所以左边1个点,右边1个点,贡献就是$1 \times 1 = 1$

对于$3$,无法构成一条链,贡献就是$0$

所以加起来就是$10$,和样例输出一样

注意从小到大所有权值必须在一条链上,如果不够成一条链,比如$3$,最小整数就是$0$,相当于没有贡献了

简化模型,只考虑一条链

![](https://img2020.cnblogs.com/blog/1975074/202004/1975074-20200411124321447-892340971.png)


设$dp_{i,j}$为从$i$到$j$的$S$最大值

然后把左边的点数后右边点数的积加上中间的链的dp值,中间的dp值就可以用递归实现.

注意这里的递归可以使用记忆化搜索.

```cpp
#include <cstdio>
#include <cstring>
#define max(a, b) ((a) > (b) ? (a) : (b))
const int maxn = 3005;
long long dp[maxn][maxn], cnt[maxn][maxn], ans;
int fa[maxn][maxn], head[maxn << 1], next[maxn << 1], to[maxn << 1], n, x, y, ct;
void dfs(int x, int f, int root) {
    cnt[root][x] = 1;
    fa[root][x] = f;
    for (int i = head[x]; i; i = next[i]) {
        if (to[i] == f) continue;
        dfs(to[i], x, root);
        cnt[root][x] += cnt[root][to[i]];
    }
}
long long dpf(int x, int y) {
    if (x == y) return 0;
    if (dp[x][y] != -1) return dp[x][y];
    return dp[x][y] = cnt[y][x] * cnt[x][y] + max(dpf(fa[y][x], y), dpf(x, fa[x][y]));
}
int main() {
    scanf("%d", &n);
    for (int i = 1; i < n; i++) {
        scanf("%d%d", &x, &y);
        to[++ct] = --y, next[ct] = head[--x], head[x] = ct;
        to[++ct] = x, next[ct] = head[y], head[y] = ct;
    }
    for (int i = 0; i < n; i++) dfs(i, -1, i);
    memset(dp, -1, sizeof(dp));
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++) ans = max(ans, dpf(i, j));
    printf("%lld", ans);
}
```
