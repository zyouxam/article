# 题目
Volodya is an odd boy and his taste is strange as well. It seems to him that a positive integer number is beautiful if and only if it is divisible by each of its nonzero digits. We will not argue with this and just count the quantity of beautiful numbers in given ranges.

Volodya是个奇怪的男孩，他的品味也很奇怪。 在他看来，当且仅当正整数可以被其每个非零数字整除时，它才是Beautiful的。 我们不会对此争论，而只计算给定范围内的 Beautiful number的数量。

# 输入格式

The first line of the input contains the number of cases $ t (1 \le t \le 10)$. Each of the next t lines contains two natural numbers $l_i$ and $r_i (1 \le li \le ri \le 9 \times 10^{18})$.

Please, do not use %lld specificator to read or write 64-bit integers in C++. It is preffered to use cin (also you may use %I64d).

输入的第一行包含数据组数$t(1 \le t \le 10)$, 接下来的t行中的每行包含两个自然数$l_i $和$r_i(1 \le l_i \le r_i \le 9 \times 10 ^ {18})$.

请不要使用％lld在C ++中读取或写入64位整数。 建议使用cin（也可以使用％l64d）.

# 输出格式

Output should contain tt numbers — answers to the queries, one number per line — quantities of beautiful numbers in given intervals (from $l_{i}$ to $r_i$, inclusively).

t行，每行输出从$l_{i}$ 到 $r_i$的Beautiful number的数量

# 题解

这道题是数位DP<span class="heimu">我好像没学过啊</span>,而且还是黑题...最开始真是一点思路都没有,后来看了dalao们的题解才能写出来.

这道题让求一段范围内的,比较容易想到的就是差分,求出$1$到$r$的数量,再求出$1$到$l-1$的数量,相减即可.

注意每位数都是$1-9$,他们的最小公倍数是$2520$,所以如果一个数可以整除$2520$,就一定可以整除$1-9$.

设$dp[i][j][k]$,$i$表示还剩下多少位数要规划,这前$i$位数模$2520$的余数是$j$,这前$i$位数的最小公倍数是$k$.

如果最后的数能整除$k$，那么它一定能整除它各位数字的最小公倍数

有可能出现的$k$都是$2520$的因数,所以将$j$取模$2520$,若所得的结果整除$k$，那么原来的数也一定整除$k$.

所以就可以得到状态转移方程

$dp[i][j][k]= \Sigma_{x=1}^{x_{max}} \ \ \ \  dp[i−1][(j \times 10+x) \% 2520][lcm(k,x)]$


$lcm(a,b)$表示$a$和$b$的最小公倍数

但是如果开这么大的数组,会MLE,所以需要继续优化,注意第三维是$lcm(k,x)$,是从$1-9$之间取数字做$lcm$运算,得到的结果数量远远少于$2520$,所以与处理一下做一个离散化即可.

希望有一天能自己独立写出黑题.

# 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
int number[20], mp[2521], cnt;
long long dp[20][2521][50],l, r, t;
int gcd(int a, int b) { return b == 0 ? a : gcd(b, a % b); }
long long dfs(int length, int pre, int mod, bool limit ,long long ans = 0) {
    if (!length) return pre % mod == 0;
    if (!limit && dp[length][pre][mp[mod]] != -1) return dp[length][pre][mp[mod]];
    int ed = limit ? number[length] : 9;
    for (int i = 0; i <= ed; i++)   
        ans += dfs(length - 1, (pre * 10 + i) % 2520, i == 0 ? mod : mod * i / gcd(mod, i), limit && i == ed);
    if (!limit) dp[length][pre][mp[mod]] = ans;
    return ans;
}
long long solve(long long n, int length = 0) {
    while (n) number[++length] = n % 10, n /= 10;
    return dfs(length, 0, 1, 1);
}
int main() {
    memset(dp, -1, sizeof(dp));
    for (int i = 1; i <= 2520; i++)
        if (!(2520 % i)) mp[i] = ++cnt;
    cin >> t;
    while (t--) {
        cin >> l >> r;
        cout << solve(r) - solve(l - 1) << endl;
    }
    return 0;
}
```

<span class="heimu">代码如有雷同,不是巧合</span>
