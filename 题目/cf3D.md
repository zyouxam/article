# 题目

This is yet another problem on regular bracket sequences.

A bracket sequence is called regular, if by inserting "+" and "1" into it we get a correct mathematical expression. For example, sequences "(())()", "()" and "(()(()))" are regular, while ")(", "(()" and "(()))(" are not. You have a pattern of a bracket sequence that consists of characters "(", ")" and "?". You have to replace each character "?" with a bracket so, that you get a regular bracket sequence.

For each character "?" the cost of its replacement with "(" and ")" is given. Among all the possible variants your should choose the cheapest.


给一个序列，序列里面会有左括号、问号、右括号。对于一个?而言，可以将其替换为一个(，也可以替换成一个)，但是都有相应的代价。问：如何替换使得代价最小。前提是替换之后的序列中，括号是匹配的。如果不能替换为一个括号匹配的序列则输出-1。

# 输入格式

The first line contains a non-empty pattern of even length, consisting of characters "(", ")" and "?". Its length doesn't exceed $5·10^4$. Then there follow $m$ lines, where $m$ is the number of characters "?" in the pattern. Each line contains two integer numbers $a_i$ and $b_i (1 ≤ a_i,  b_i ≤ 10^6)$, where $a_i$ is the cost of replacing the i-th character "?" with an opening bracket, and $b_i$ — with a closing one.

第一行是序列，序列长度不超过50000，下面m(m是?的数量)行有每行2个数据，第一个是(的代价，第2个是)的代价

# 输出格式

Print the cost of the optimal regular bracket sequence in the first line, and the required sequence in the second.

Print -1, if there is no answer. If the answer is not unique, print any of them.

第一行打印代价，第二行打印替换后的序列。不行输出-1

# 样例输入

```
(??)
1 2
2 8
```

# 样例输出

```
4
()()
```

# 题解

使用贪心

先把每个?变成右括号，如果这时候发现这个右括号没有左括号和它匹配，就从当前位置往前在所有没变成左括号的?中选择变为左括号产生的花费最少的一个,转成左括号.

写代码的时候, 扫描每个?,将它赋值为左括号, 然后将其加入优先队列, 找花费最少的时候, 直接从优先队列里找

如果最后还是存在未匹配的括号，就是无解的情况.

另外统计价值之和时要开long long.

# 代码

```cpp
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <queue>
const int MAXN = 50005;
char s[MAXN];
int n, lb, rb;
struct Node {
    int i, l, r;
    bool operator<(const Node &other) const { return r - l < other.r - other.l; }
};
std::priority_queue<Node> pq;
int main() {
    scanf("%s", s + 1);
    n = strlen(s + 1);
    int cnt = 0;
    long long sum = 0;
    for (int i = 1; i <= n; i++) {
        if (s[i] == '(') cnt++;
        if (s[i] == '?') {
            scanf("%d%d", &lb, &rb);
            if (i == 1) {
                sum += lb;
                s[i] = '(';
                cnt++;
                continue;
            }
            sum += rb;
            s[i] = ')';
            pq.push((Node){i, lb, rb});
        }
        if (s[i] == ')') {
            if (cnt == 0) {
                if (pq.empty()) return puts("-1"), 0;
                Node t = pq.top();
                pq.pop();
                if (t.i == n) t = pq.top(), pq.pop();
                sum = sum - t.r + t.l;
                s[t.i] = '(';
                cnt += 2 - (t.i == i);
            }
            if (s[i] == ')') cnt--;
        }
    }
    if (cnt != 0)puts("-1");
    else printf("%lld\n%s", sum, s + 1);
    return 0;
}
```
