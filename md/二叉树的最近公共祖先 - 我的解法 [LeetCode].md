> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [zhuanlan.zhihu.com](https://zhuanlan.zhihu.com/p/48963142)

最近刷题刷到了这道题：二叉树的最近公共祖先。

我想记录一下我的解法。

先贴一下原题：

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

[百度百科](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/%25E6%259C%2580%25E8%25BF%2591%25E5%2585%25AC%25E5%2585%25B1%25E7%25A5%2596%25E5%2585%2588/8918834%3Ffr%3Daladdin)中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。”

例如，给定如下二叉树: root = [3,5,1,6,2,0,8,null,null,7,4]

```
_______3______
       /              \
    ___5__          ___1__
   /      \        /      \
   6      _2       0       8
         /  \
         7   4
```

这确实是一道很好的题目，从不同角度看待这个问题有不同的解法。

这道题目在剑指 offer 上面作为面试案例出现的，提出了两种主要的思路。

**第一种是递归解法：**

给定 root，遍历一棵树，如果两个结点 p、q 分别在 root 的左右子树中，那么 root 就是最近公共祖先。否则，两个结点 p、q 同在左子树或者右子树中，这个时候将左子树（右子树）作为 root 进行递归。

这种方法复杂度太高，需要多次遍历树的部分。

**第二种是将这个问题转化为链表的公共节点问题：**

遍历这一棵树，找到两个结点 p、q 并且将 root 到 p,q 的路径分别存在两个链表里面，然后就可以将这个问题转化为链表的公共节点问题。

这种方法复杂度可以接受，但是需要额外的内存。

**我的解法**

以上面的那个二叉树为例，假设 p,q 节点分别为 6,4。

显然这个寻找两个节点的最近公共祖先的问题最好使用自底向上的方法，也就是后序遍历。

使用后序遍历的时候，我们一直往左，找到了 p 节点，然后用一个指针 P1 指向这个节点，在后续的操作中，由于是后序遍历，P1 指针可以沿着 p 节点的父节点往上。同理，找到 q 节点，然后用一个指针 P2 指向这个节点，在后续的操作中，由于是后序遍历，P2 指针也可以沿着 q 节点的父节点往上。当 P1 指针和 P2 指针指向同一个节点的时候，这个节点就是 p,q 两个节点的最近公共祖先。

![](https://pic2.zhimg.com/v2-7b7b6de9bbf5f414d85e6e09004a81e1_b.jpg)

下面贴一下代码：

```
class Solution {
private:
    TreeNode* p_father;
    TreeNode* q_father;
    TreeNode* ancestor;
    bool flag;
public:
    Solution():flag(false),ancestor(NULL),p_father(NULL),q_father(NULL){};
    void inorderTraversal(TreeNode* root, TreeNode* p, TreeNode* q){
        if(root == NULL)
            return ;
        if(root->val == p->val)
            p_father = p;
        if(root->val == q->val)
            q_father = q;
        inorderTraversal(root->left, p, q);
        inorderTraversal(root->right, p, q);
        
        if(p_father != NULL && (p_father == root->left || p_father == root->right) )
            p_father = root;
            
        if(q_father != NULL && (q_father == root->left || q_father == root->right))
            q_father = root;
        
        if(p_father != NULL && q_father != NULL)
            if(p_father == q_father && flag == false){
                flag = true;
                ancestor = p_father;
            }
            
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == NULL && p == NULL && q == NULL)
            return root;
        inorderTraversal(root, p, q);
        return ancestor;
    }
};
```

这个解法复杂度比较低，也只需要常数的内存。上面的代码由于使用的递归方式进行的后序遍历，一定会遍历一整颗树，所以时间上是有冗余的，最好是找到最近公共祖先就立刻停止遍历，可以用迭代的后序遍历来实现。