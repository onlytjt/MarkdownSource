###二叉查找树(Binary Search Tree, BST)

一个典型的二叉查找树如果不为空，怎么一定有如下性质：

* 若任意节点的左子树不空，则左子树上所有结点的值均小于它的根结点的值
* 若任意节点的右子树不空，则右子树上所有结点的值均大于它的根结点的值
* 任意节点的左、右子树也分别为二叉查找树
*  没有键值相等的节点

对一棵二叉查找树进行中序遍历会得到一个有序序列，如下图所示，进行中序便利后会得到ACEHRSX。

![pic1](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/BST_1.jpg)

典型的树节点类应包括键值，值，以及左右孩子。

```java
// 典型树节点类
public class Node<Key, Value> {
  	Key key;
  	Value value;
  	Node left;
  	Node right;
}
```

这里我们为了简洁，将其简化，去掉了值，仅保留了在查找比较操作中关键的键值。

```java
// 本文使用的二叉树节点类
public class TreeNode<T> {
    public T data;
    public TreeNode left;
    public TreeNode right;
}
```

####1. BST的插入与构造

二叉查找树的构造过程实质上就是查询与插入的动态过程。

* 若插入节点为空，则直接new插入
* 若待插入键值K小于节点键值，则递归插入左子树
* 若待插入键值K大于节点键值，则递归插入右子树









#### 删除节点

二叉查找树的删除操作比较复杂

1. 待删除节点为叶节点
2. 待删除节点没有左子树或没有右子树
3. 待删除节点有左右子树

​	(a) 待删除节点为z，将其记录暂存为tmp。`z=tmp`

​	(b) 找到z的后继，即z右子树中最小的节点y，用y替换z。`z=y`

​	(c) 连接的左右子树。`z.left=tmp.left. z.right=tmp.right`

​	(d) 在连接后的右子树中删除y。`z.right.delete(y)`