> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/silence250627170/archive/2011/07/24/2115692.html)

    暑假了，计划要把数据结构复习下，把以前忘记的补回来，以前课堂跳过去的内容再看看，一些题目再做下；我们的教材是清华大学出版社的《数据结构 c 语言版》-- 严蔚敏、吴伟民，今天看到第三章 “栈和队列” 了，其中栈的部分记忆比较清晰，不过有一个 “八皇后问题” 书上提了下，没有详细说明，我自个儿在网上查了查，看了会没怎么看懂，也不懂国际象棋，就没怎么管而是去做那个汉诺塔的题了，比较简单就不用贴代码了。

　　看到双端队列 (deque) 了，书上是这样说的：除了栈和队列外还有一种限定性数据结构是双端队列；双端队列是限定插入和删除操作在表的两端进行的线性表；尽管双端队列看起来似乎比栈和队列更灵活，但实际上在应用程序中远不及栈和队列有用，故在此不作详细讨论。于是乎就跳了过去讲链队列和循环队列去了，我想反正是复习嘛，没有考试的压力，不要放过就是了，再说就算双端队列远不及栈和队列有用，也要知道怎么个没用法。所以就查了下:

　　                                              **Deque 成员函数**

<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td width="33%">函数<br></td><td width="66%">描述</td></tr><tr><td>c.assign(beg,end)<br>c.assign(n,elem)<br></td><td>将 [beg; end) 区间中的数据赋值给 c。<br>将 n 个 elem 的拷贝赋值给 c。</td></tr><tr><td>c.at(idx)<br></td><td>传回索引 idx 所指的数据，如果 idx 越界，抛出 out_of_range。</td></tr><tr><td>c.back()<br></td><td>传回最后一个数据，不检查这个数据是否存在。</td></tr><tr><td>c.begin()<br></td><td>传回迭代器重的可一个数据。</td></tr><tr><td>c.clear()<br></td><td>移除容器中所有数据。</td></tr><tr><td>deque&lt;Elem&gt; c<br>deque&lt;Elem&gt; c1(c2)<br>Deque&lt;Elem&gt; c(n)<br>Deque&lt;Elem&gt; c(n, elem)<br>Deque&lt;Elem&gt; c(beg,end)<br>c.~deque&lt;Elem&gt;()<br></td><td>创建一个空的 deque。<br>复制一个 deque。<br>创建一个 deque，含有 n 个数据，数据均已缺省构造产生。<br>创建一个含有 n 个 elem 拷贝的 deque。<br>创建一个以 [beg;end) 区间的 deque。<br>销毁所有数据，释放内存。</td></tr><tr><td>c.empty()<br></td><td>判断容器是否为空。</td></tr><tr><td>c.end()<br></td><td>指向迭代器中的最后一个数据地址。</td></tr><tr><td>c.erase(pos)<br>c.erase(beg,end)<br></td><td>删除 pos 位置的数据，传回下一个数据的位置。<br>删除 [beg,end) 区间的数据，传回下一个数据的位置。</td></tr><tr><td>c.front()<br></td><td>传回地一个数据。</td></tr><tr><td>get_allocator<br></td><td>使用构造函数返回一个拷贝。</td></tr><tr><td>c.insert(pos,elem)<br>c.insert(pos,n,elem)<br>c.insert(pos,beg,end)<br></td><td>在 pos 位置插入一个 elem 拷贝，传回新数据位置。<br>在 pos 位置插入 &gt; n 个 elem 数据。无返回值。<br>在 pos 位置插入在 [beg,end) 区间的数据。无返回值。</td></tr><tr><td>c.max_size()<br></td><td>返回容器中最大数据的数量。</td></tr><tr><td>c.pop_back()<br></td><td>删除最后一个数据。</td></tr><tr><td>c.pop_front()<br></td><td>删除头部数据。</td></tr><tr><td>c.push_back(elem)<br></td><td>在尾部加入一个数据。</td></tr><tr><td>c.push_front(elem)<br></td><td>在头部插入一个数据。</td></tr><tr><td>c.rbegin()<br></td><td>传回一个逆向队列的第一个数据。</td></tr><tr><td>c.rend()<br></td><td>传回一个逆向队列的最后一个数据的下一个位置。</td></tr><tr><td>c.resize(num)<br></td><td>重新指定队列的长度。</td></tr><tr><td>c.size()<br></td><td>返回容器中实际数据的个数。</td></tr><tr><td>C1.swap(c2)<br>Swap(c1,c2)<br></td><td>将 c1 和 c2 元素互换。<br>同上操作。</td></tr><tr><td>operator[]</td><td>返回容器中指定位置的一个引用。</td></tr></tbody></table>

又搜到了一个使用 C++ STL 容器基本操作的博客 [http://hi.baidu.com/xuehuo_0411/blog/item/c225942c0a02033a349bf7d1.html](http://hi.baidu.com/xuehuo_0411/blog/item/c225942c0a02033a349bf7d1.html)，后我照着一些简单的程序写了遍，

代码如下：

![](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)双端队列容器 (deque) [![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1 /*
 2 2011.7.24
 3 使用c++ STL库的双端队列容器(deque)的小程序
 4 看了博客 http://hi.baidu.com/xuehuo_0411/blog/item/c225942c0a02033a349bf7d1.html 后照着写的
 5 msdn的文档是 http://msdn.microsoft.com/zh-cn/library/bb398188.aspx
 6 其中还有些没有写了，其他重要函数
 7 d1.swap(d2);//d1和d2交换
 8 zm.max_size();//可支持的最大deque元素个数
 9 zm.front();//首元素
10 zm.back();//末元素
11 */
12 #include <iostream>
13 #include <deque>
14 #include <iterator>
15 using namespace std;
16 void viewByArrey(deque<int> zm);//用数组的方式访问deque元素
17 void viewByIterator(deque<int> zm);//用迭代器的方式來訪問deque元素
18 void insertFrontAndCenter(deque<int> zm); //头部和中间位置插入deque元素
19 int main(){    
20     deque<int> zm;
21     //viewByArrey(zm);
22     //viewByIterator(zm);
23     insertFrontAndCenter(zm);//头部和中间位置插入deque元素
24     system("pause");
25     }
26 void viewByArrey(deque<int> zm){//用数组的方式访问deque元素
27     zm.push_back(26);
28     zm.push_back(11);
29     zm.push_back(12);
30     for(int i = 0; i < zm.size();i++){
31         cout<<zm[i]<<endl;
32         }
34     }
35 void viewByIterator(deque<int> zm){
36     zm.push_back(111);
37     zm.push_back(222);
38     zm.push_back(333);
39     deque<int>::iterator aa;//定义了迭代器aa
40     int i = 0;
41     for(aa = zm.begin(),i = 0;aa != zm.end();aa++,i++){
42         cout<<aa[0]<<"\t"<<*aa<<endl;
43         //cout<<*aa<<endl;
44         }
45     }
46 void insertFrontAndCenter(deque<int> zm){//头部和中间位置插入deque元素
47     zm.push_back(111);
48     zm.push_back(222);
49     zm.push_front(333);//放到前面
50     zm.insert(zm.begin()+2,444);//理论上是放到111后面
51     //看是不是这个顺序333,111,444,222
52     for(int i = 0; i < zm.size();i++){
53         cout<<zm[i]<<",";
54         }
55     cout<<endl;
56     }
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

亲手写了后，觉得 deque 还是很好用的啊，就搜了下 “C++ STL deque 的缺点”，觉得这人讲的不错，还做了对比 [http://blog.csdn.net/ianleelj/article/details/3939354](http://blog.csdn.net/ianleelj/article/details/3939354)，内容不多就贴下吧，

 1 **vector**

    向量 相当于一个数组  
    在内存中分配一块连续的内存空间进行存储。支持不指定 vector 大小的存储。STL 内部实现时，首先分配一个非常大的内存空间预备进行存储，即 capacituy（）函数返回的大小，当超过此分配的空间时再整体重新放分配一块内存存储，这给人以 vector 可以不指定 vector 即一个连续内存的大小的感觉。通常此默认的内存分配能完成大部分情况下的存储。  
   优点：(1) 不指定一块内存大小的数组的连续存储，即可以像数组一样操作，但可以对此数组  
               进行动态操作。通常体现在 push_back() pop_back()  
               (2) 随机访问方便，即支持 [ ] 操作符和 vector.at()  
               (3) 节省空间。  
   缺点：(1) 在内部进行插入删除操作效率低。  
               (2) 只能在 vector 的最后进行 push 和 pop，不能在 vector 的头进行 push 和 pop。  
               (3) 当动态添加的数据超过 vector 默认分配的大小时要进行整体的重新分配、拷贝与释  
                     放   
2 **list**  
    双向链表  
    每一个结点都包括一个信息快 Info、一个前驱指针 Pre、一个后驱指针 Post。可以不分配必须的内存大小方便的进行添加和删除操作。使用的是非连续的内存空间进行存储。  
   优点：(1) 不使用连续内存完成动态操作。  
               (2) 在内部方便的进行插入和删除操作  
               (3) 可在两端进行 push、pop  
   缺点：(1) 不能进行内部的随机访问，即不支持 [] 操作符和 vector.at()  
               (2) 相对于 verctor 占用内存多  
  
3 **deque**  
   双端队列 double-end queue  
   deque 是在功能上合并了 vector 和 list。  
   优点：(1) 随机访问方便，即支持 [ ] 操作符和 vector.at()  
               (2) 在内部方便的进行插入和删除操作  
               (3) 可在两端进行 push、pop  
   缺点：(1) 占用内存多  
**  
使用区别：**  
     1 如果你需要高效的随即存取，而不在乎插入和删除的效率，使用 vector   
     2 如果你需要大量的插入和删除，而不关心随即存取，则应使用 list   
     3 如果你需要随即存取，而且关心两端数据的插入和删除，则应使用 deque

　　看了后觉得 deque 还是很好用的嘛，虽然内存占用多些，但是也不至于像书上那样说的一无是处，deque 像是集 vector 和 list 两者特性的一个容器，牺牲内存换的功能的完善也没什么不可以吧。

ps: 第一次写编程的随笔，有些不知道说什么，肤浅之处望大侠们指出。