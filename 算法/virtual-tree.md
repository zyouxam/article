虚树用于将一棵树的无意义点删除， 只保留关键点和树的结构， 优化树形dp的速度。

# 构建虚树

初始化一个栈， 将根节点入栈(必须保留根节点以供遍历)， 然后根据$dfn$序遍历这颗树。



遍历途中把关键点依次入栈, 当要添加一个新的关键点($v$)时， 求$v$与栈顶($stk[top]$)的$lca(v, stk[top])$，此时有几种情况：

1. $lca(v, stk[top]) = stk[top]$ ，直接入栈。

![](https://img2020.cnblogs.com/blog/1975074/202007/1975074-20200704203751186-914420964.png)

2. $lca(v, stk[top])\  != stk[top]$

![](https://img2020.cnblogs.com/blog/1975074/202007/1975074-20200704204707917-1787976957.png)

此时$stk[top]$所在子树必定已经处理完毕， 所以可以开始构建虚树。

将$stk[top]$和$stk[top - 1]$连边， 然后将$stk[top]$出栈， 接下来原来的$stk[top - 1]$变成$stk[top]$,  然后如此循环， 直到$stk[top-1]$深度小于等于$lca(v, stk[top])$。

 如果$lca(v, stk[top])$在栈内即$stk[top-1]$深度等于$lca(v, stk[top])$，将$stk[top]$与$lca(v, stk[top])$连边， $stk[top]$出栈即可。

如果$lca(v, stk[top])$不在栈内即$stk[top-1]$深度小于$lca(v, stk[top])$，

![](https://img2020.cnblogs.com/blog/1975074/202007/1975074-20200704205736288-1053738324.png)

*图中 lca 指 lca(v, stk[top])*

因为此时需要保留树的结构， 所以将$stk[top]$与$lca(v, stk[top])$连边， $stk[top]$出栈，$lca(v, stk[top])$入栈， 向$v$方向继续遍历。

![](https://img2020.cnblogs.com/blog/1975074/202007/1975074-20200704210147409-721256849.png)

*处理完成后的情况*

此时左子树已经完全出栈， 栈内只存在一条链。

遍历完之后， 栈内也只存在一条链， 依次退栈， 也要把$stk[top]$与$stk[top-1]$连边。
