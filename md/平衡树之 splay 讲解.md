> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/BLADEVIL/p/3464458.html)

　　首先来说是 splay 是二叉搜索树，它可以说是线段树和 SBT 的综合，更可以解决一些二者解决不了的问题，splay 几乎所有的操作都是由 splay 这一操作完成的，在介绍这一操作前我们先介绍几个概念和定义

　　二叉搜索树，即 BST(binary search tree)，这样的树有一个关键字，满足对于每个节点来说，以该节点左儿子为根节点的子树中的所有节点的关键字小于该节点的关键字，以该节点右儿子为根节点的子树中的所有节点的关键字大于该节点的关键字。

　　splay 主要可以用来解决区间的维护问题

　　假设我们需要维护一个数列，支持

　　1. 在数列第 i 位后插入一个长为 l 的数列

　　2. 在数列第 i 为后删除一个长为 l 的数列　

　　3. 将数列的 l r 区间翻转（1 2 3  2 3 翻转后为 3 2 3 2 1）

　　4. 将数列的 l r 区间同时加上一个值

　　5. 将数列的 l r 区间同时改为一个值

　　6. 求数列的 l r 区间的和（最大值）

　　其实线段树上的大部分操作这里都支持，比如区间最大子区间和

　　首先对于当前的树，它的中序遍历就是当前的区间，每个点的关键字（二叉搜索树的那个）是内个点表示区间元素的标号，比如一个点的关键字是 3，那么这个点代表区间中第 3 个元素，每个点除了关键字外还记录了一个 tree[i] 代表这个点对应区间内的元素是什么。

![](https://images0.cnblogs.com/blog/584079/201312/08224047-907c3b116a134252a5c1f8b3745a19a3.png)

　　上图（节点内的数代表 tree 值）的树表示数列 3 7 1 4 2 -1

　　对于每个节点的记录内容为

　　son[x,0..1] 左右儿子

　　father[x] 父亲节点

　　还有我们定义 root 为当前树的根节点，sroot 为超级节点（-1），sroot 只连接着 root（其实就是定义了 root 的 father 为 - 1）

　　那么我们首先建树的时候，具体过程为

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
function build(l,r:longint):longint;
var
    mid                            :longint;
begin
    mid:=(l+r) div 2;
    tree[mid]:=a[mid];//a为区间的值
    if l<=mid-1 then 
    begin
          son[mid,0]:=build(l,mid-1);
          father[son[mid,0]]:=mid;
    end;
    if mid+1<=r then 
    begin
          son[mid,1]:=build(mid+1,r);
          father[son[mid,1]]:=mid;
    end;
    update(mid);//可暂时忽略
    exit(mid);
end;
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

　　那么我们现在有了一颗树，我们还需要改变这棵树的形态，就是 splay（x,y）代表将编号为 x 的点旋转到 y 的儿子处，那么我们就需要介绍一个旋转操作了，在介绍旋转操作之前还应该引入一个 find 操作，假设我们需要找区间内第 i 个元素，树中代表这个点的编号是多少（每个点都有一个编号，编号随意定，满足互不相同就行了，类似于线段树，SBT 中的点的编号，没有实际意义）我们规定一个点的 size 值为以该点为根节点的子树的节点数，那么 find（l）表示数列中第 l 个元素在树中的编号。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
function find(x:longint):longint;
var 
    t                        :longint;
begin
    t:=root;    
    while true do
    begin
        push_down(t);//可暂时忽略
        if size[son[t,0]]+1=x then exit(t);
        if size[son[t,0]]+1>x then t:=son[t,0]
        else
            begin
                dec(x,size[son[t,0]]+1);
                t:=son[t,1];
            end;
    end;
end;
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

　　那么我们介绍旋转过程 rotate(x,y) 代表将编号为 x 的节点旋转到他的父亲节点，就是如果 x 是左儿子就右旋 father[x]，右儿子就左旋 father[x]，y 代表 x 是他父亲的左节点（0）还是右节点（1）。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
procedure rotate(x,y:longint);
var 
    f                        :longint;
begin
    push_down(x);
    f:=father[x];
    father[son[x,y xor 1]]:=f;
    son[f,y]:=son[x,y xor 1];
    if f=root then root:=x
    else
        if f=son[father[f],0] then 
            son[father[f],0]:=x else 
            son[father[f],1]:=x;
    father[x]:=father[f];
    father[f]:=x;
    son[x,y xor 1]:=f;
    update(f);
    update(x);
end;
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

　　那么对于 splay 过程我们就可以理解了

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
procedure splay(x,y:longint);
var 
    u, v                    :longint;
begin
    while father[x]<>y do
        if father[father[x]]=y then 
            rotate(x,ord(x=son[father[x],1])) else
        begin
            if son[father[x],0]=x then u:=1 else u:=-1;
            if son[father[father[x]],0]=father[x] then v:=1 else v:=-1;
            if u*v=1 then
            begin
                rotate(father[x],ord(x=son[father[x],1]));
                rotate(x,ord(x=son[father[x],1]));
            end else
            begin
                rotate(x,ord(x=son[father[x],1]));
                rotate(x,ord(x=son[father[x],1]));
            end;
        end;
    update(x);
end;
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

　　其中 u=1 代表 x 是父亲的左节点，u=-1 代表是右节点，v=1 代表 x 父亲是 x 爷爷的左节点，v=-1 代表右节点

　　那么 v*u=1 的情况就是 x 和父亲，爷爷，祖孙三代是一条链（直观的说）这种情况先旋父亲，再旋 x，否则旋两次 x, 其实结果是一样的，但是前人证明这样操作会使 splay 树更平衡些。

　　那么剩下的操作就是基于这几个操作的扩展了，比如添加区间，在 l 后加入长 s 的区间

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
for i:=n+1 to n+s do read(a[i]);
p:=build(n+1,n+s);//把这一区间建成一棵树我们只需要插入p节点就行了
q:=find(l); splay(q,sroot);
q:=find(l+1); splay(q,root);
son[son[root,1],0]:=p;
father[p]:=son[root,1];
update(son[root,1]);
update(root);
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

　　其中两个 find 和 splay 操作是精华，我们先找到第 l 个元素，旋转到根，再找到第 l+1 个元素，旋转到根的右儿子，那么第 l+1 个节点是没有左儿子的（因为当前以 l 为根，l+1 元素左儿子代表比 l 大的，比 l+1 小的，显然没有），那么我们不是要在 L 后面插入区间么，就直接将 p 点当成 l+1 点的左儿子就行了。

　　那么我们会发现，假如我要在区间的开头插入区间怎么办 find（0）是没有值的，那么我们就插入左右标兵，在最开始建树的时候 inc（n），root:=build(0,n)；

　　其实这样多插入了两个数，那么我们要 find(l) 时需要 find(l+1)，以后每次用 find 的时候 + 1 就好了

　　那么对于删除操作假设删除 l r 区间

```
p:=find(l); splay(p,sroot);
p:=find(r+2); splay(p,root);
son[son[root,1],0]:=-1;
update(son[root,1]);
update(root);
```

　　我们将区间中第 l-1 个元素旋转到根节点，r+1 个元素旋转到根节点的右儿子，那么以 son[son[root,1],0] 为根节点的子树代表的就是区间 l r，直接删除就好，那么对于区间最大值操作，类似于线段树就行了，因为旋转后树的结构已经改变了，那么我们需要维护节点存储的信息，就是 update 操作

```
procedure update(x:longint);
begin
    sum[x]:=sum[son[x,0]]+tree[x]+sum[son[x,1]];
    size[x]:=size[son[x,0]]+1+size[son[x,1]];
    max[x]:=get_max(tree[x],get_max(max[son[x,0]],max[son[x,1]]));
end;
```

　　对于区间赋值，修改这样的，打标签就好了，那么对于区间翻转操作我们也可以打标签，flag[x] 为 true 代表以 x 为根节点的区间需要翻转，那么我们旋转一个区间的时候，假设根节点为 x。

```
proceudre reverse(x:longint);
begin
     swap(son[x,1],son[x,0]);
     flag[son[x,1]]:=not flag[son[x,1]];
     flag[son[x,0]]:=not flag[son[x,0]];
end;
```

　　可以自己举个例子，发现满足这个性质

　　push_down 操作则为下放标签

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
procedure push_down(x:longint);
var 
    l,r                        :longint;
begin
    l:=son[x,0];r:=son[x,1];
    if flag[x] then
        begin
            if l<>-1 then renew(l,0);
            if r<>-1 then renew(r,0);
            flag[x]:=false;
        end;
    if val[x]<>0 then
        begin
            if l<>-1 then renew(l,val[x]);
            if r<>-1 then renew(r,val[x]);
            val[x]:=0;
        end;
end;
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")