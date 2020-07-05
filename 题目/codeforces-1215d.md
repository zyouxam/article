[Codeforces 1215D Ticket Game 原题](https://codeforces.com/problemset/problem/1215/D)

# 题目  

Monocarp and Bicarp live in Berland, where every bus ticket consists of n digits (n is an even number). During the evening walk Monocarp and Bicarp found a ticket where some of the digits have been erased. The number of digits that have been erased is even.

Monocarp and Bicarp have decided to play a game with this ticket. Monocarp hates happy tickets, while Bicarp collects them. A ticket is considered happy if the sum of the first n2 digits of this ticket is equal to the sum of the last n2 digits.

Monocarp and Bicarp take turns (and Monocarp performs the first of them). During each turn, the current player must replace any erased digit with any digit from 0 to 9. The game ends when there are no erased digits in the ticket.

If the ticket is happy after all erased digits are replaced with decimal digits, then Bicarp wins. Otherwise, Monocarp wins. You have to determine who will win if both players play optimally.

一张票有n位数，如果这张票的前一半数字的和等于后一半数字的和(n一定是偶数)，就称这张票为快乐票。有些数被擦除了，标记为’?’(’?‘的个数也是偶数)，现在Monocarp 和 Bicarp 进行一个游戏，两人轮流将’?'变换成0到9的任意一个数，Monocarp先手，如果最后票为快乐票则Bicarp赢，否则Monocarp赢。

# 输入格式

The first line contains one even integer $n (2≤n≤2⋅10^5)$ — the number of digits in the ticket.

The second line contains a string of n digits and "?" characters — the ticket which Monocarp and Bicarp have found. If the i-th character is "?", then the i-th digit is erased. Note that there may be leading zeroes. The number of "?" characters is even.

先输入数字长度

然后输入这个数字,可能包括问号

# 输出格式

If Monocarp wins, print "Monocarp" (without quotes). Otherwise print "Bicarp" (without quotes).

谁赢输出谁的名字

# 样例

## 输入1

```
4
0523
```
## 输出1

```
Bicarp
```

## 输入2

```
2
??
```

## 输出2

```
Bicarp
```

## 输入3

```
8
?054??0?
```

## 输出3

```
Bicarp
```

## 输入4

```
6
???00?
```

## 输出4

```
Monocarp
```

# 题解

三种情况

1. 如果最开始左半部分数字之和等于右半部分数字之和,左右问号数量相等Bicarp赢;数量不等Monocarp赢.

应该很好理解,Monocarp先手,无论Monocarp填什么,Bicarp的最优策略都是在另一边填一样的,这样可以一直保证左右数字之和相等,因为Bicarp最后一个填,所以Bicarp赢.

当然,如果数量不等,Bicarp自然赢不了.

--------

**当两边数字和不等,且Monocarp可以在数字和大的部分填数字时,他一定会填9以拉开差距,那么Bicarp如果能在另半部分填,一定也会填9.**

**这就是两部分的问号不断抵消的情况,抵消到只有一边有问号为止,这时候就可以继续思考了,下面所有情况都是假定只有一边有问号**

2. 如果左半部分数字之和大于右半部分数字之和

若只有左边有问号,无论Bicarp如何决策也无法使左半部分减少,右半部分又无法增加,那么Monocarp赢

若只有右边有问号,那么Monocarp一定会填0拉开差距,而Bicarp一定会填9,那么看一下Bicarp能填的数量即可,若能填$n$个问号,$n \times 9$与左右之差相等,Bicarp赢,反之Monocarp赢.

注意写代码时,"只有左边有问号"的情况可以和"只有右边有问号"的情况合并,因为这时候看作右边的问号数量为负数即可,为负数肯定无法与正数的数量差相等,Monocarp赢一定会赢.


3. 如果左半部分数字之和小于右半部分数字之和

可以看作为第2种情况的镜像,同理可以解决

# 代码

```cpp
#include <bits/stdc++.h>
#define t(i) int(((double)(i) / (n)) + 0.5)
using namespace std;
char s[200005];
int main() {
    int n, mark[2] = {0, 0}, sum[2] = {0, 0};
    scanf("%d%s", &n, s);
    for (int i = 0; i < n; i++)
        if (s[i] == '?') mark[t(i)]++;
        else sum[t(i)] += s[i] - '0';
    puts((mark[0] == mark[1] && sum[0] == sum[1] ||
          sum[0] > sum[1] && sum[0] - sum[1] == (mark[1] - mark[0]) / 2 * 9 ||
          sum[1] > sum[0] && sum[1] - sum[0] == (mark[0] - mark[1]) / 2 * 9) ? "Bicarp" : "Monocarp");
    return 0;
}
```
