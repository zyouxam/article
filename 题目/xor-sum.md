# 题目

You are given a positive integer $N(1≦N≦10^{18})$. Find the number of the pairs of integers $u$ and $v (0≦u,v≦N)$ such that there exist two non-negative integers $a$ and $b$ satisfying $a xor b=u$ and $a+b=v$. Here, $xor$ denotes the bitwise exclusive OR. Since it can be extremely large, compute the answer modulo $10^9+7$.

给出正整数$N$,求出整数对$u$和$v (0≤u,v≤N)$的数目，使得存在两个非负整数$a$和$b$满足$a xor b = u$ 和$a + b= v$。这里，$xor$表示按位异或。对答案取模$10^9 + 7$

#　输入格式

The input is given from Standard Input in the following format:　$N$

一个整数$N$

#　输出格式

Print the number of the possible pairs of integers $u$ and $v$ ,modulo $10^9+7$.

$u,v$对的数量模$10^9+7$

#　输入样例

```
3
```

# 输出样例

```
5
```

# 题解

把$n=1,2,3,4,5...$的答案手算出来, 是`1, 2, 4, 5, 8, 10, 13, 14, 18, 21, 26, 28, 33, 36, 40, 41, 46, 50, 57, 60...`然后找规律, 如果不好找, 可以在[这个网站](http://oeis.org/?language=chineseS)搜索.

用记忆化搜索优化效率, 如果开数组开不下, 用map即可

~~我怀疑这个不是正解~~

# 代码

```cpp
#include <cstdio>
#include <map>
const long long MOD = 1e9 + 7;
std::map<long long, long long> dp;
long long f(long long x) {
    if (dp[x]) return dp[x] % MOD;
    return dp[x] = (f((x - 1) / 2) + f(x / 2) + f((x - 2) / 2)) % MOD;
}
int main() {
    long long n;
    scanf("%lld", &n);
    dp[0] = 1;
    dp[1] = 2;
    printf("%lld\n", f(n) % MOD);
}
```