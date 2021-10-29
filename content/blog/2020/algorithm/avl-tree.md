+++
title = "AVL Tree 概述"
description = "AVL 树是一种高度平衡的二叉搜索树"
author = "gra55"
categories = ["Algorithm"]
tags = ["avl-tree", "binary-tree", "tree", "2020"]
date = "2020-01-10"
+++

# 0x00 前言

最早出现的是**二叉树**，随后人们发现二叉树可以用来二分查找，所以出现了**二叉搜索树**。

但是**二叉搜索树**在极端情况会退化成单链表的形式，所以出现了**平衡二叉搜索树**，即**AVL 树**（AVL 取自发明它的两个人的名字首字母，话说老外都爱这个干，以自己名字命名各种算法）

但是**AVL 树**也是有缺陷的，删除和插入效率很低（因为需要旋转多次），所以出现了**红黑树**，**红黑树**不是严格的平衡树，所以查找效率可能会低一点。

本文主要介绍**AVL 树**，后续出单独的文章介绍**红黑树**。

# 0x01 特性

+ 必须是一颗二叉搜索树
+ 每个节点的左子树和右子树的高度差的绝对值不能大于 1
+ 查找、插入、删除的平均和最坏时间复杂度都是 O(logn)

# 0x02 术语

平衡因子（Balance Factor）
+ 二叉树节点的左子树高度减去右子树高度的值，称为该节点的平衡因子

最小不平衡子树
+ 距离插入节点最近的，且平衡因子绝对值大于 1 的节点为根的树，就是最小不平衡子树

# 0x03 实现

## 节点结构

下面的节点结构包含了节点的高度，也可以存储平衡因子和父节点。

```python
class TreeNode(object):
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
        self.height = 0
```

## AVL 类提供的函数

失衡调整 - 左单旋（在最小不平衡子树的右子树中插入右孩子时）

```python
     +---+              +---+                 +---+
     | 4 |              | 4 |                 | 5 |
     +---+              +---+                 +---+
         |                  |                 |   |
         +---+              +---+         +---+   +---+
         | 5 |              | 5 |         | 4 |   | 6 |
         +---+              +---+         +---+   +---+
                                |
                                +---+
                                | 6 |
                                +---+

def leftRotation(proot):
    """
    单左旋转操作
    :param proot: 最小失衡子树的根节点
    :rtype: TreeNode
    """
    # 左旋
    tmpNode = proot.right
    proot.right = tmpNode.left
    tmpNode.left = proot

    # 更新节点高度
    proot.height = max(proot.left.height, proot.right.height) + 1
    tmpNode.height = max(tmpNode.left.height, tmpNode.right.height) + 1
    
    return tmpNode
```

失衡调整 - 右单旋（在最小不平衡子树的左子树中插入左孩子时）

```python
             +---+                     +---+
             | 5 |                     | 5 |
             +---+                     +---+
             |   |                     |   |
         +---+   +---+             +---+   +---+
         | 4 |   | 6 |             | 3 |   | 6 |
         +---+   +---+             +---+   +---+
         |                         |   |
     +---+                     +---+   +---+
     | 3 |                     | 2 |   | 4 |
     +---+                     +---+   +---+
     |
 +---+
 | 2 |
 +---+

def rightRotation(proot):
    """
    单右旋转操作
    :param proot: 最小失衡子树的根节点
    :rtype: TreeNode
    """
    # 右旋
    tmpNode = proot.left
    proot.left = tmpNode.right
    tmpNode.right = proot

    # 更新节点高度
    proot.height = max(proot.left.height, proot.right.height) + 1
    tmpNode.height = max(tmpNode.left.height, tmpNode.right.height) + 1

    return tmpNode
```

失衡调整 - 右左旋（在最小不平衡子树的右子树中插入左孩子时）

```python
        +----------+                   +----------+                      +----------+
        |     5    |                   |     5    |                      |     5    |
        +----------+                   +----------+                      +----------+
        |          |                   |          |                      |          |
    +---+          +---+           +---+          +---+              +---+          +---+
    | 3 |          | 6 |           | 3 |          | 6 |              | 3 |          | 7 |
    +---+          +---+           +---+          +---+              +---+          +---+
    |   |              |           |   |              |              |   |          |   |
+---+   +---+          +---+   +---+   +---+          +---+      +---+   +---+  +---+   +---+
| 2 |   | 4 |          | 8 |   | 2 |   | 4 |          | 7 |      | 2 |   | 4 |  | 6 |   | 8 |
+---+   +---+          +---+   +---+   +---+          +---+      +---+   +---+  +---+   +---+
                       |                                  |
                   +---+                                  +---+
                   | 7 |                                  | 8 |
                   +---+                                  +---+

def rightLeftRotation(proot):
    """
    右左旋转操作
    :param proot: 最小失衡子树的根节点
    :rtype: TreeNode
    """
    # 右旋，以 proot.right 为根进行旋转
    proot.right = rightRotation(proot.right)
    # 左旋
    return leftRotation(proot)
```

失衡调整 - 左右旋（在最小不平衡子树的左子树中插入右孩子时）

```python
            +----------+                        +----------+                     +----------+
            |     5    |                        |     5    |                     |     5    |
            +----------+                        +----------+                     +----------+
            |          |                        |          |                     |          |
        +---+          +---+                +---+          +---+             +---+          +---+
        | 3 |          | 7 |                | 3 |          | 7 |             | 3 |          | 7 |
        +---+          +---+                +---+          +---+             +---+          +---+
        |   |          |   |                |   |          |   |             |   |          |   |
    +---+   +---+  +---+   +---+        +---+   +---+  +---+   +---+     +---+   +---+  +---+   +---+
    | 2 |   | 4 |  | 6 |   | 8 |        | 2 |   | 4 |  | 6 |   | 8 |     | 1 |   | 4 |  | 6 |   | 8 |
    +---+   +---+  +---+   +---+        +---+   +---+  +---+   +---+     +---+   +---+  +---+   +---+
    |                                   |                                |   |
+---+                               +---+                            +---+   +---+
| 0 |                               | 1 |                            | 0 |   | 2 |
+---+                               +---+                            +---+   +---+
    |                               |
    +---+                       +---+
    | 1 |                       | 0 |
    +---+                       +---+

def leftRightRotation(proot):
    """
    左右旋转操作
    :param proot: 最小失衡子树的根节点
    :rtype: TreeNode
    """
    # 左旋，以 proot.left 为根进行旋转
    proot.left = leftRotation(proot.left)
    # 右旋
    return rightRotation(proot)
```

插入节点

```python
def insert(pnode, key):
    """
    插入节点：递归来插入

    :param pnode: 插入的树
    :param key: 插入的值
    :return rootNode: 返回插入后的根节点
    :rtype: TreeNode
    """

    if pnode is None:  # 递归终止条件
        pnode = TreeNode(key)
    elif key > pnode.value:  # 插入的值大于当前节点的值
        pnode.right = insert(pnode.right, key)
        if abs(pnode.right.height - pnode.left.height) > 1:  # 插入后失衡
            if key > pnode.right.value:  # 左单旋
                pnode = leftRotation(pnode)
            elif key < pnode.right.value:  # 右左旋
                pnode = rightLeftRotation(pnode)

    elif key < pnode.value:  # 插入的值小于当前节点的值
        pnode.left = insert(pnode.left, key)
        if abs(pnode.left.height - pnode.right.height) > 1: # 插入导致失衡
            if key < pnode.left.value:  # 右单旋
                pnode = rightRotation(pnode)
            elif key > pnode.left.value:
                pnode = leftRightRotation(pnode)

    pnode.height = max(pnode.left.height, pnode.right.height) + 1
    return pnode
```

删除节点

+ 删除节点也可能导致AVL树的失衡，实际上删除节点和插入节点是一种互逆的操作：
  + 删除右子树的节点导致AVL树失衡时，相当于在左子树插入节点导致AVL树失衡。
  + 删除左子树的节点导致AVL树失衡时，相当于在右子树插入节点导致AVL树失衡。

```c++
/*删除指定元素*/
template<typename T>
AVLTreeNode<T>* AVLTree<T>::remove(AVLTreeNode<T>* & pnode, T key)
{
    if (pnode != nullptr)
    {
        if (key == pnode->key)            //找到删除的节点
        {
            //因AVL也是二叉排序树，删除节点要维护其二叉排序树的条件
            if (pnode->lchild != nullptr&&pnode->rchild != nullptr)        //若左右都不为空
            {
                // 左子树比右子树高,在左子树上选择节点进行替换
                if (height(pnode->lchild) > height(pnode->rchild))
                {
                    //使用左子树最大节点来代替被删节点，而删除该最大节点
                    AVLTreeNode<T>* ppre = maximum(pnode->lchild);        //左子树最大节点
                    pnode->key = ppre->key;                                //将最大节点的值覆盖当前结点
                    pnode->lchild = remove(pnode->lchild, ppre->key);    //递归地删除最大节点
                }
                else  //在右子树上选择节点进行替换
                {
                    //使用最小节点来代替被删节点，而删除该最小节点
                    AVLTreeNode<T>* psuc = minimum(pnode->rchild);        //右子树的最小节点
                    pnode->key = psuc->key;                                //将最小节点值覆盖当前结点
                    pnode->rchild = remove(pnode->rchild, psuc->key);    //递归地删除最小节点
                }
 
            }
            else
            {
                AVLTreeNode<T> * ptemp = pnode;
                if (pnode->lchild != nullptr)
                    pnode = pnode->lchild;
                else if (pnode->rchild != nullptr)
                    pnode = pnode->rchild;
                delete ptemp;
                return nullptr;
            }
 
        }
        else if (key > pnode->key)//要删除的节点比当前节点大，则在右子树进行删除
        {
            pnode->rchild =  remove(pnode->rchild, key);
            //删除右子树节点导致不平衡:相当于情况二或情况四
            if (height(pnode->lchild) - height(pnode->rchild) == 2) 
            {
                //相当于在左子树上插入右节点造成的失衡（情况四）
                if (height(pnode->lchild->rchild)>height(pnode->lchild->lchild))
                    pnode = leftRightRotation(pnode);
                else//相当于在左子树上插入左节点造成的失衡（情况二）
                    pnode = rightRotation(pnode); 
            }
        }
        else if (key < pnode->key)//要删除的节点比当前节点小，则在左子树进行删除
        {
            pnode->lchild= remove(pnode->lchild, key);
             //删除左子树节点导致不平衡：相当于情况三或情况一
            if (height(pnode->rchild) - height(pnode->lchild) == 2)
            {
                //相当于在右子树上插入左节点造成的失衡（情况三）
                if (height(pnode->rchild->lchild)>height(pnode->rchild->rchild))
                    pnode = rightLeftRotation(pnode);
                else//相当于在右子树上插入右节点造成的失衡（情况一）
                    pnode = leftRotation(pnode); 
            }
        }
        return pnode;
    }
    return nullptr;
};
```

---
参考：

:pushpin:  [数据结构图文解析之：AVL树详解及C++模板实现](https://cloud.tencent.com/developer/article/1155143)
