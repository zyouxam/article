# 欧拉函数

欧拉函数： 小于或等于n的正整数中与n互质的数的数目

通式：$\phi(n) = n \Pi_{p_i}(1-\frac 1 {p_i})$ ($p_i$为小于或等于n的正整数中与n互质的数) ， 特殊的，$\phi(1)=1$ 。

由通式可得， $\phi(n)=(1-\frac 1 {p_1}) \cdot (1-\frac 1 {p_2}) \cdot \dots \cdot (1-\frac 1 {p_{i-1}}) \cdot (n-\frac n {p_i})  $


```cpp
int phi(int n){
	int ans=n;
	for(int i=2;i*i<=n;++i)
		if(n%i==0) { // 第一次遇到的可整除的必定为质因子
			ans-=ans/i; //依次乘每项
			while(n%i==0)n/=i; // 除完所有质因子， 保证第4行正确性
		}
	if(n!=1)ans-=ans/n; // 大于sqrt(n)的最多只有一个质因子
	return ans;
}
```

对于倒数第三行， 小于$\sqrt n$的所有质因子相乘若小于$\sqrt n$, 那么有且只有一个大于$\sqrt n$的质因子， 假设有两个， 相乘必定会大于$n$.
