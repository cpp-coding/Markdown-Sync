> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/huangxincheng/archive/2012/07/30/2614484.html)

       我们知道，二叉查找树相对来说比较容易形成最坏的链表情况，所以前辈们想尽了各种优化策略，包括 AVL，红黑，以及今天

要讲的 Treap 树。

       Treap 树算是一种简单的优化策略，这名字大家也能猜到，树和堆的合体，其实原理比较简单，在树中维护一个 " 优先级 “，” 优先级“

采用随机数的方法，但是”优先级 “必须满足根堆的性质，当然是“大根堆” 或者 “小根堆” 都无所谓，比如下面的一棵树：

![](https://pic002.cnblogs.com/images/2012/214741/2012073000522678.png)

从树中我们可以看到：

①：节点中的 key 满足 “二叉查找树”。

②：节点中的 “优先级” 满足小根堆。

一：基本操作

1：定义

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1     #region Treap树节点
 2     /// <summary>
 3     /// Treap树
 4     /// </summary>
 5     /// <typeparam ></typeparam>
 6     /// <typeparam ></typeparam>
 7     public class TreapNode<K, V>
 8     {
 9         /// <summary>
10         /// 节点元素
11         /// </summary>
12         public K key;
14         /// <summary>
15         /// 优先级（采用随机数）
16         /// </summary>
17         public int priority;
19         /// <summary>
20         /// 节点中的附加值
21         /// </summary>
22         public HashSet<V> attach = new HashSet<V>();
24         /// <summary>
25         /// 左节点
26         /// </summary>
27         public TreapNode<K, V> left;
29         /// <summary>
30         /// 右节点
31         /// </summary>
32         public TreapNode<K, V> right;
34         public TreapNode() { }
36         public TreapNode(K key, V value, TreapNode<K, V> left, TreapNode<K, V> right)
37         {
38             //KV键值对
39             this.key = key;
40             this.priority = new Random(DateTime.Now.Millisecond).Next(0,int.MaxValue);
41             this.attach.Add(value);
43             this.left = left;
44             this.right = right;
45         }
46     }
47     #endregion
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

节点里面定义了一个 priority 作为 “堆定义” 的旋转因子，因子采用“随机数“。

2：添加

    首先我们知道各个节点的 “优先级” 是采用随机数的方法，那么就存在一个问题，当我们插入一个节点后，优先级不满足“堆定义 " 的

时候我们该怎么办，前辈说此时需要旋转，直到满足堆定义为止。

旋转有两种方式，如果大家玩转了 AVL，那么对 Treap 中的旋转的理解轻而易举。

①： 左左情况旋转

![](https://pic002.cnblogs.com/images/2012/214741/2012073001112626.png)

从图中可以看出，当我们插入 “节点 12” 的时候，此时 “堆性质” 遭到破坏，必须进行旋转，我们发现优先级是 6<9，所以就要进行

左左情况旋转，最终也就形成了我们需要的结果。

②： 右右情况旋转

![](https://pic002.cnblogs.com/images/2012/214741/2012073001162452.png)

既然理解了”左左情况旋转 “，右右情况也是同样的道理，优先级中发现“6<9"，进行” 右右旋转“最终达到我们要的效果。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1         #region 添加操作
 2         /// <summary>
 3         /// 添加操作
 4         /// </summary>
 5         /// <param ></param>
 6         /// <param ></param>
 7         public void Add(K key, V value)
 8         {
 9             node = Add(key, value, node);
10         }
11         #endregion
13         #region 添加操作
14         /// <summary>
15         /// 添加操作
16         /// </summary>
17         /// <param ></param>
18         /// <param ></param>
19         /// <param ></param>
20         /// <returns></returns>
21         public TreapNode<K, V> Add(K key, V value, TreapNode<K, V> tree)
22         {
23             if (tree == null)
24                 tree = new TreapNode<K, V>(key, value, null, null);
26             //左子树
27             if (key.CompareTo(tree.key) < 0)
28             {
29                 tree.left = Add(key, value, tree.left);
31                 //根据小根堆性质，需要”左左情况旋转”
32                 if (tree.left.priority < tree.priority)
33                 {
34                     tree = RotateLL(tree);
35                 }
36             }
38             //右子树
39             if (key.CompareTo(tree.key) > 0)
40             {
41                 tree.right = Add(key, value, tree.right);
43                 //根据小根堆性质，需要”右右情况旋转”
44                 if (tree.right.priority < tree.priority)
45                 {
46                     tree = RotateRR(tree);
47                 }
48             }
50             //将value追加到附加值中（也可对应重复元素）
51             if (key.CompareTo(tree.key) == 0)
52                 tree.attach.Add(value);
54             return tree;
55         }
56         #endregion
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

3: 删除

  跟普通的二叉查找树一样，删除结点存在三种情况。

①：叶子结点

      跟普通查找树一样，直接释放本节点即可。

②：单孩子结点

     跟普通查找树一样操作。

③：满孩子结点

    其实在 treap 中删除满孩子结点有两种方式。

第一种：跟普通的二叉查找树一样，找到 “右子树” 的最左结点（15），拷贝元素的值，但不拷贝元素的优先级，然后在右子树中

           删除 “结点 15” 即可，最终效果如下图。

![](https://pic002.cnblogs.com/images/2012/214741/2012073001332339.png)

第二种：将”结点下旋 “，直到该节点不是” 满孩子的情况“，该赋 null 的赋 null，该将孩子结点顶上的就顶上，如下图：

![](https://pic002.cnblogs.com/images/2012/214741/2012073001450711.png)

当然从理论上来说，第二种删除方法更合理，这里我写的就是第二种情况的代码。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1         #region 删除当前树中的节点
 2         /// <summary>
 3         /// 删除当前树中的节点
 4         /// </summary>
 5         /// <param ></param>
 6         /// <returns></returns>
 7         public void Remove(K key, V value)
 8         {
 9             node = Remove(key, value, node);
10         }
11         #endregion
13         #region 删除当前树中的节点
14         /// <summary>
15         /// 删除当前树中的节点
16         /// </summary>
17         /// <param ></param>
18         /// <param ></param>
19         /// <returns></returns>
20         public TreapNode<K, V> Remove(K key, V value, TreapNode<K, V> tree)
21         {
22             if (tree == null)
23                 return null;
25             //左子树
26             if (key.CompareTo(tree.key) < 0)
27             {
28                 tree.left = Remove(key, value, tree.left);
29             }
30             //右子树
31             if (key.CompareTo(tree.key) > 0)
32             {
33                 tree.right = Remove(key, value, tree.right);
34             }
35             /*相等的情况*/
36             if (key.CompareTo(tree.key) == 0)
37             {
38                 //判断里面的HashSet是否有多值
39                 if (tree.attach.Count > 1)
40                 {
41                     //实现惰性删除
42                     tree.attach.Remove(value);
43                 }
44                 else
45                 {
46                     //有两个孩子的情况
47                     if (tree.left != null && tree.right != null)
48                     {
49                         //如果左孩子的优先级低就需要“左旋”
50                         if (tree.left.priority < tree.right.priority)
51                         {
52                             tree = RotateLL(tree);
53                         }
54                         else
55                         {
56                             //否则“右旋”
57                             tree = RotateRR(tree);
58                         }
60                         //继续旋转
61                         tree = Remove(key, value, tree);
62                     }
63                     else
64                     {
65                         //如果旋转后已经变成了叶子节点则直接删除
66                         if (tree == null)
67                             return null;
69                         //最后就是单支树
70                         tree = tree.left == null ? tree.right : tree.left;
71                     }
72                 }
73             }
75             return tree;
76         }
77         #endregion
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

4: 总结

treap 树在 CURD 中是期望的 logN，由于我们加了”优先级 “, 所以会出现” 链表“的情况几乎不存在，但是他的 Add 和 Remove 相比严格的

平衡二叉树有更少的旋转操作，可以说性能是在”普通二叉树 “和” 平衡二叉树“之间。

最后是总运行代码，不过这里我就不做测试了。

![](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code

```
1 using System;
  2 using System.Collections.Generic;
  3 using System.Linq;
  4 using System.Text;
  6 namespace DataStruct
  7 {
  8     #region Treap树节点
  9     /// <summary>
 10     /// Treap树
 11     /// </summary>
 12     /// <typeparam ></typeparam>
 13     /// <typeparam ></typeparam>
 14     public class TreapNode<K, V>
 15     {
 16         /// <summary>
 17         /// 节点元素
 18         /// </summary>
 19         public K key;
 21         /// <summary>
 22         /// 优先级（采用随机数）
 23         /// </summary>
 24         public int priority;
 26         /// <summary>
 27         /// 节点中的附加值
 28         /// </summary>
 29         public HashSet<V> attach = new HashSet<V>();
 31         /// <summary>
 32         /// 左节点
 33         /// </summary>
 34         public TreapNode<K, V> left;
 36         /// <summary>
 37         /// 右节点
 38         /// </summary>
 39         public TreapNode<K, V> right;
 41         public TreapNode() { }
 43         public TreapNode(K key, V value, TreapNode<K, V> left, TreapNode<K, V> right)
 44         {
 45             //KV键值对
 46             this.key = key;
 47             this.priority = new Random(DateTime.Now.Millisecond).Next(0,int.MaxValue);
 48             this.attach.Add(value);
 50             this.left = left;
 51             this.right = right;
 52         }
 53     }
 54     #endregion
 56     public class TreapTree<K, V> where K : IComparable
 57     {
 58         public TreapNode<K, V> node = null;
 60         #region 添加操作
 61         /// <summary>
 62         /// 添加操作
 63         /// </summary>
 64         /// <param ></param>
 65         /// <param ></param>
 66         public void Add(K key, V value)
 67         {
 68             node = Add(key, value, node);
 69         }
 70         #endregion
 72         #region 添加操作
 73         /// <summary>
 74         /// 添加操作
 75         /// </summary>
 76         /// <param ></param>
 77         /// <param ></param>
 78         /// <param ></param>
 79         /// <returns></returns>
 80         public TreapNode<K, V> Add(K key, V value, TreapNode<K, V> tree)
 81         {
 82             if (tree == null)
 83                 tree = new TreapNode<K, V>(key, value, null, null);
 85             //左子树
 86             if (key.CompareTo(tree.key) < 0)
 87             {
 88                 tree.left = Add(key, value, tree.left);
 90                 //根据小根堆性质，需要”左左情况旋转”
 91                 if (tree.left.priority < tree.priority)
 92                 {
 93                     tree = RotateLL(tree);
 94                 }
 95             }
 97             //右子树
 98             if (key.CompareTo(tree.key) > 0)
 99             {
100                 tree.right = Add(key, value, tree.right);
102                 //根据小根堆性质，需要”右右情况旋转”
103                 if (tree.right.priority < tree.priority)
104                 {
105                     tree = RotateRR(tree);
106                 }
107             }
109             //将value追加到附加值中（也可对应重复元素）
110             if (key.CompareTo(tree.key) == 0)
111                 tree.attach.Add(value);
113             return tree;
114         }
115         #endregion
117         #region 第一种：左左旋转（单旋转）
118         /// <summary>
119         /// 第一种：左左旋转（单旋转）
120         /// </summary>
121         /// <param ></param>
122         /// <returns></returns>
123         public TreapNode<K, V> RotateLL(TreapNode<K, V> node)
124         {
125             //top：需要作为顶级节点的元素
126             var top = node.left;
128             //先截断当前节点的左孩子
129             node.left = top.right;
131             //将当前节点作为temp的右孩子
132             top.right = node;
134             return top;
135         }
136         #endregion
138         #region 第二种：右右旋转（单旋转）
139         /// <summary>
140         /// 第二种：右右旋转（单旋转）
141         /// </summary>
142         /// <param ></param>
143         /// <returns></returns>
144         public TreapNode<K, V> RotateRR(TreapNode<K, V> node)
145         {
146             //top：需要作为顶级节点的元素
147             var top = node.right;
149             //先截断当前节点的右孩子
150             node.right = top.left;
152             //将当前节点作为temp的右孩子
153             top.left = node;
155             return top;
156         }
157         #endregion
159         #region 树的指定范围查找
160         /// <summary>
161         /// 树的指定范围查找
162         /// </summary>
163         /// <param ></param>
164         /// <param ></param>
165         /// <returns></returns>
166         public HashSet<V> SearchRange(K min, K max)
167         {
168             HashSet<V> hashSet = new HashSet<V>();
170             hashSet = SearchRange(min, max, hashSet, node);
172             return hashSet;
173         }
174         #endregion
176         #region 树的指定范围查找
177         /// <summary>
178         /// 树的指定范围查找
179         /// </summary>
180         /// <param ></param>
181         /// <param ></param>
182         /// <param ></param>
183         /// <returns></returns>
184         public HashSet<V> SearchRange(K min, K max, HashSet<V> hashSet, TreapNode<K, V> tree)
185         {
186             if (tree == null)
187                 return hashSet;
189             //遍历左子树（寻找下界）
190             if (min.CompareTo(tree.key) < 0)
191                 SearchRange(min, max, hashSet, tree.left);
193             //当前节点是否在选定范围内
194             if (min.CompareTo(tree.key) <= 0 && max.CompareTo(tree.key) >= 0)
195             {
196                 //等于这种情况
197                 foreach (var item in tree.attach)
198                     hashSet.Add(item);
199             }
201             //遍历右子树（两种情况：①:找min的下限 ②：必须在Max范围之内）
202             if (min.CompareTo(tree.key) > 0 || max.CompareTo(tree.key) > 0)
203                 SearchRange(min, max, hashSet, tree.right);
205             return hashSet;
206         }
207         #endregion
209         #region 找到当前树的最小节点
210         /// <summary>
211         /// 找到当前树的最小节点
212         /// </summary>
213         /// <returns></returns>
214         public TreapNode<K, V> FindMin()
215         {
216             return FindMin(node);
217         }
218         #endregion
220         #region 找到当前树的最小节点
221         /// <summary>
222         /// 找到当前树的最小节点
223         /// </summary>
224         /// <param ></param>
225         /// <returns></returns>
226         public TreapNode<K, V> FindMin(TreapNode<K, V> tree)
227         {
228             if (tree == null)
229                 return null;
231             if (tree.left == null)
232                 return tree;
234             return FindMin(tree.left);
235         }
236         #endregion
238         #region 找到当前树的最大节点
239         /// <summary>
240         /// 找到当前树的最大节点
241         /// </summary>
242         /// <returns></returns>
243         public TreapNode<K, V> FindMax()
244         {
245             return FindMin(node);
246         }
247         #endregion
249         #region 找到当前树的最大节点
250         /// <summary>
251         /// 找到当前树的最大节点
252         /// </summary>
253         /// <param ></param>
254         /// <returns></returns>
255         public TreapNode<K, V> FindMax(TreapNode<K, V> tree)
256         {
257             if (tree == null)
258                 return null;
260             if (tree.right == null)
261                 return tree;
263             return FindMax(tree.right);
264         }
265         #endregion
267         #region 删除当前树中的节点
268         /// <summary>
269         /// 删除当前树中的节点
270         /// </summary>
271         /// <param ></param>
272         /// <returns></returns>
273         public void Remove(K key, V value)
274         {
275             node = Remove(key, value, node);
276         }
277         #endregion
279         #region 删除当前树中的节点
280         /// <summary>
281         /// 删除当前树中的节点
282         /// </summary>
283         /// <param ></param>
284         /// <param ></param>
285         /// <returns></returns>
286         public TreapNode<K, V> Remove(K key, V value, TreapNode<K, V> tree)
287         {
288             if (tree == null)
289                 return null;
291             //左子树
292             if (key.CompareTo(tree.key) < 0)
293             {
294                 tree.left = Remove(key, value, tree.left);
295             }
296             //右子树
297             if (key.CompareTo(tree.key) > 0)
298             {
299                 tree.right = Remove(key, value, tree.right);
300             }
301             /*相等的情况*/
302             if (key.CompareTo(tree.key) == 0)
303             {
304                 //判断里面的HashSet是否有多值
305                 if (tree.attach.Count > 1)
306                 {
307                     //实现惰性删除
308                     tree.attach.Remove(value);
309                 }
310                 else
311                 {
312                     //有两个孩子的情况
313                     if (tree.left != null && tree.right != null)
314                     {
315                         //如果左孩子的优先级低就需要“左旋”
316                         if (tree.left.priority < tree.right.priority)
317                         {
318                             tree = RotateLL(tree);
319                         }
320                         else
321                         {
322                             //否则“右旋”
323                             tree = RotateRR(tree);
324                         }
326                         //继续旋转
327                         tree = Remove(key, value, tree);
328                     }
329                     else
330                     {
331                         //如果旋转后已经变成了叶子节点则直接删除
332                         if (tree == null)
333                             return null;
335                         //最后就是单支树
336                         tree = tree.left == null ? tree.right : tree.left;
337                     }
338                 }
339             }
341             return tree;
342         }
343         #endregion
344     }
345 }
```