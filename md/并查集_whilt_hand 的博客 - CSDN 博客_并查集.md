> 并查集（分析 + 板子）所谓并查集，顾名思义，就是对于集合上的操作，包括合并集合和查找集合。

并查集（分析 + 板子）
------------

所谓并查集，顾名思义，就是对于集合上的操作，包括合并集合和查找集合。

首先定义一个 parent 数组，用来记录下标结点的父亲结点，如 parent[2]=5, 代表 2 号结点的父亲结点为 5 号结点。数组初始化值为 i 即本身（即 parent[i]=i;), 初始化后每个点父亲结点都是自己本身，初始化也依题而异，有时初始化为 - 1。

1、查找操作

```cpp
int find_pa(int x)
{
   if(parent[x]==x) return x;//父节点为本身，直接返回。
   return parent[x]=find_pa(parent[x]); //类似记忆化储存.
}
```

合并操作

```cpp
void union_(int x , int y )
{
     int x_root=find_pa(x);
     int y_root=find_pa(y);
     if(x_root==y_root) return ;    //两节点父节点是同一个，即两节点在同一个集合中，无需合并
     else {
     parent[x_root]=y_root;     //合并操作，可让其中任意一个父节点的父节点为对方的父节点；
     return ;
     }
}
```

![](https://img-blog.csdnimg.cn/20210304104649337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doaWx0X2hhbmQ=,size_16,color_FFFFFF,t_70#pic_center)

以树结构举例，存入树时，像 1，2 相连，则可以合并 1，2 两个结点，初始时 parent[2]=2, 合并后 parent[2]=1; 将整棵树合并后，(find_pa(1)=find_pa(2)=’’……’=find_pa(9))，但不一定是等于 1，这与存入的顺序有关。

**优化**  
我们对于之前的合并操作是任意合并的，可能出现所有点连接在一条直线上的情况，此时查询操作的时间开销就比较大了

![](https://img-blog.csdnimg.cn/20210305112933699.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doaWx0X2hhbmQ=,size_16,color_FFFFFF,t_70#pic_center)

同样一组数据，按照不同的合并方式呈现出来的形态完全不同，我们当然更希望是树形的，这样再次查询根节点时所用时间开销就小。  
这时我们就要用到我们的一个小小优化，用一个 dist 数组，存储根结点的最大深度（从下往上的深度）当需要合并时，先进行判断，谁的根深度小，我们自然是希望深度小的连接到深度大的根下面  
![](https://img-blog.csdnimg.cn/20210305155953153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doaWx0X2hhbmQ=,size_16,color_FFFFFF,t_70#pic_center)

```cpp
void union_(int x,int y)
{
  int x_root=find_pa(x);
  int y_root=find_pa(y);
  if(x_root==y_root) return;
  else{  //合并
  if(dist[x_root]<dist[y_root]) parent[x_root]=y_root;
  else if(dist[y_root]<dist[x_root]) parent[y_root]=x_root;
  else{//两个的根节点深度一样，任意选择一个为另一个的根结点，此时根节点深度+1
   parent[x_root]=y_root;
   dist[y_root]++;
   }
   }
```

.  
.  
.  
**例题：洛谷 P1551**  
[亲戚](https://www.luogu.com.cn/problem/P1551)  
**题目背景**  
若某个家族人员过于庞大，要判断两个是否是亲戚，确实还很不容易，现在给出某个亲戚关系图，求任意给出的两个人是否具有亲戚关系。  
**题目描述**  
规定：x 和 y 是亲戚，y 和 z 是亲戚，那么 x 和 z 也是亲戚。如果 x,y 是亲戚，那么 x 的亲戚都是 y 的亲戚，y 的亲戚也都是 x 的亲戚。  
**输入格式**  
第一行：三个整数 n,m,p，（n<=5000,m<=5000,p<=5000），分别表示有 n 个人，m 个亲戚关系，询问 p 对亲戚关系。  
以下 m 行：每行两个数 Mi，Mj，1<=Mi，Mj<=N，表示 Mi 和 Mj 具有亲戚关系。  
接下来 p 行：每行两个数 Pi，Pj，询问 Pi 和 Pj 是否具有亲戚关系。  
输出格式 P 行，每行一个’Yes’或’No’。表示第 i 个询问的答案为 “具有” 或“不具有”亲戚关系。

**输入输出样例**  
输入：  
6 5 3  
1 2  
1 5  
3 4  
5 2  
1 3  
1 4  
2 3  
5 6

输出：  
Yes  
Yes  
No

**分析**  
题意很明显直接上并查集版子，是亲戚就合并到一个集合。  
以样例分析：  
![](https://img-blog.csdnimg.cn/20210304112057324.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doaWx0X2hhbmQ=,size_16,color_FFFFFF,t_70#pic_center)  
样例给出的关系如图所示，1，2，3，4，5 是一个集合，有亲戚关系，而 6 与任何人都没有亲戚关系。当合并完后，find_pa(1)=find_pa(2)=find_pa(x=3)=find_pa(x4)=find_pa(5); 具体等于多少不重要，与输入样例顺序有关，只要能确定在一个集合就行。  
find_pa(6)=6；

```cpp
#include <bits/stdc++.h>
using namespace std;
const int Max=2e5+20;
inline int read()
{
    int f=1,x=0;char ch;
    do{ch=getchar();if(ch=='-')f=-1;}
    while(ch<'0'||ch>'9');
    do{x=x*10+ch-'0';ch=getchar();}
    while(ch>='0'&&ch<='9');return x*f;
}
int parent[Max],dist[Max];
int n,m;
int find_pa(int x)
{
    if(parent[x]==x) return x;
    return parent[x]=find_pa(parent[x]);
}
void union_(int x,int y)
{
    int x_root=find_pa(x);
    int y_root=find_pa(y);
    if(x_root==y_root) return ;
    else {
        if(dist[x]>dist[y]) parent[y_root]=x_root;
        else if(dist[y]>dist[x]) parent[x_root]=y_root;
        else {parent[x_root]=y_root;dist[y_root]++;}
    }
}
int main()
{
    for(int i=0;i<=Max;i++)
        parent[i]=i;     //初始化自己的亲戚是自己
    n=read(),m=read();
    int p=read();
    int x,y;
    for(int i=1;i<=m;i++)
    {
       x=read(),y=read();
       union_(x,y);  //合并两个具有亲戚关系的人
    }
    for(int i=1;i<=p;i++)
    {
        x=read(),y=read();
        if(find_pa(x)==find_pa(y)) cout<<"Yes"<<endl; //是否在同一集合中
        //注意不能换成parent[x]==parent[y]，读者可以自行考虑下原因
        else cout<<"No"<<endl;
    }
    return 0;
 }
```