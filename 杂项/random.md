

验证程序正确性时， 我们经常使用对拍， 比较常见的对拍程序类似这个：

```cpp
#include<cstdlib>
int main()  {  
    while(1) {  
        system("./data > data.in");  
        system("./std < data.in > std.out");  
        system("./test < data.in > test.out");  
        if(system("diff std.out test.out"))
            break;  
    } 
    return 0;  
}  
```
这里的数据生成程序是调用的外部程序， 每个数据点都重新运行， 而在linux系统上， `time(0)`会返回当前时间戳， 即从1970年1月1日0点0分0秒到现在经过的秒数， 所以每秒`time(0)`的返回值不变， 作为随机数种子时， 每秒内生成的数据也都相同， 那么该怎么修改呢？

首先我们要知道linux系统上一个特殊的设备`/dev/urandom`， 它提供永不为空的随机二进制数据流， 当然还有更随机的`/dev/random`。但是`/dev/random`为了保证随机性可能会中断输出， 相比之下， `/dev/urandom`不会导致程序堵塞。

更多信息参见[内核熵池](https://baike.baidu.com/item/%E5%86%85%E6%A0%B8%E7%86%B5%E6%B1%A0/1731396)

实际应用时， 把`/dev/urandom`当作一个文件， 使用`freopen`读入

```cpp
freopen("/dev/urandom", "r", stdin);
```

然后初始化随机数种子时， 使用`getchar()`读入:

```cpp
srand(getchar()*getchar()*getcahr()*time(0));
```
