> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/qt666/p/6508208.html)

今天打了人生第一道 ST 表题 (其实只是 ST 表跑得最快);

ST 表是一种用来解决 RMQ 问题的利器。。。

大体操作有两步：

第一部分 nlogn 预处理

第二部分 O(1) 询问

预处理就是运用倍增 + 区间动规

ST 表使用 DP 思想求解区间最值，貌似属于区间动态规划，不过区间在增加时，每次并不是增加一个长度，而是使用倍增的思想，每次增加 2^i 个长度。

使用 F[i,j] 表示以 i 为起点，区间长度为 2^j 的区间最值，此时区间为 [i,i + 2^j - 1]。

比如，F[0,2]表示区间 [0,3] 的最值，F[2,2]表示区间 [2,5] 的最值。

在求解 F[i,j]时，ST 算法是先对长度为 2^j 的区间 [i,i + 2^j - 1] 分成两等份，每份长度均为 2^(j - 1)。之后在分别求解这两个区间的最值 F[i,j - 1]和 F[i + 2^(j - 1),j - 1]。，最后在结合这两个区间的最值，求出整个区间的最值。

状态转移方程是 F[i,j] = min(F[i,j - 1],F[i + 2^(j - 1),j - 1])

初始状态为：F[i,0] = A[i]。

代码如下：

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1 void makeST()
 2 {
 3   pre[0]=1;for(int i=1;i<=20;i++) pre[i]=pre[i-1]<<1;
 4   pre2[0]=-1;for(int i=1;i<=n;i++) pre2[i]=pre2[i>>1]+1;
 5   for(int i=1;i<=n;i++) ST[i][0]=i;
 6   for(int j=1;j<=20;j++)
 7     for(int i=1;i<=n;i++){
 8       if(i+pre[j]-1<=n){
 9        int x1=ST[i][j-1],x2=ST[i+pre[j-1]][j-1];
10        if(a[x1]>a[x2]) ST[i][j]=x1;
11        else ST[i][j]=x2;
12       }
13     }
14 }
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

一开始打错了好多个地方！！！醉了！！！

1. 要把 20 的循环放在外面，手贱打错了。。。

2.pre2 数组是用来支持询问的。。。相当于是 logi, 所以是 for(1--n);

询问操作：

在预处理期间，每一个状态对应的区间长度都为 2^i。由于给出的待查询区间长度不一定恰好为 2^i，因此我们应对待查询的区间进行处理。

这里我们把待查询的区间分成两个小区间，这两个小区间满足两个条件：（1）这两个小区间要能覆盖整个区间（2）为了利用预处理的结果，要求小区间长度相等且都为 2^i。注意两个小区间可能重叠。

在程序计算求解区间长度时，并没有那么麻烦，我们可以直接得到 i，即等于直接对区间长度取以 2 为底的对数。这里，对于区间 [3,11]，其分解的区间长度为 int（log(11 - 3 + 1)） = 3，这里 log 是以 2 为底的。

根据上述思想，可以把待查询区间 [x，y] 分成两个小区间 [x，x + 2^i - 1] 和 [y - 2^i + 1，y] ，其又分别对应着 F[x,i] 和 F[y - 2^i + 1,i]，此时为了求解整个区间的最小值，我们只需求这两个值得最小值即可，此时复杂度是 O(1)。

注意细节要加 1。。。

代码实现如下：

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1 int query(int l,int r)
2 {
3   if(l==r)return l;
4   int x=pre2[r-l+1];
5   int x1=ST[l][x],x2=ST[r-pre[x]+1][x];
6   return a[x1]>a[x2]?x1:x2;
7 }
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")