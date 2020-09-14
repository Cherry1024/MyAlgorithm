#### [面试题 17.12. BiNode LCCI](https://leetcode-cn.com/problems/binode-lcci/)

> 二叉搜索树转换成单链表的形式——左子树为空、右子树为下一节点

二叉树的中序遍历问题

##### 非遍历

利用前驱节点prev，记录上一个被处理的节点，当前节点被遍历到时，将node.left置空，prev.right指向当前节点node,prev指针后移到当前节点node在，最后node进入右子树。

```java
public TreeNode convertBiNode(TreeNode root) {
    Stack<TreeNode> stack = new Stack<>();
    TreeNode prev = new TreeNode(0); //前驱节点
    TreeNode head = prev;
    TreeNode node = root; 
    stack.push(root);
    while(!stack.isEmpty()){
        TreeNode cur=stack.pop();
        if(cur!=null){
            if(cur.right!=null) stack.push(cur.right);
            stack.push(cur);
            stack.push(null);
            if(cur.left!=null) stack.push(cur.left);            
        }
        else{
            
        }
    }
}
```

#### [Binary Search Tree Iterator](https://leetcode-cn.com/problems/binary-search-tree-iterator/)

> **Note:**
>
> next() and hasNext() should run in average O(1) time and uses O(h) memory, where h is the height of the tree.
> You may assume that next() call will always be valid, that is, there will be at least a next smallest number in the BST when next() is called.

这道题不知道怎么写主要是看到实现迭代器有点蒙圈。。。对Java类的this用法已经忘掉了

📕 首先这道题是说我们有一个迭代器类，有两个方法函数`next()`和`haeNext()`，前者的作用是返回BST中下一个最小元素，后者是判断BST中是否还有元素。**本质上，迭代器可以用于迭代任何容器的对象**，本题的容器是一个BST。为数据结构设计一个迭代器的作用是逐个遍历容器的每个元素，本题要求升序返回元素，即中序遍历。

##### 扁平化二叉搜索树

迭代器是使程序员能够遍历容器的对象，实现迭代器最简单的方法是在类似数组的容器接口上，这个应该是最容易想到的方法。若我们拥有一个数组，那我们只需要一个指针或者索引便可轻松实现上述两个方法函数。

因此，扁平化BST就是基于此，使用额外的数组将树展开存放，在数组中构建迭代器。

```java
class BSTIterator{
    ArrayList<Integer> arr;
    int index;
    public BSTIterator(TreeNode root){
        this.arr = new ArrayList<Interger>();
        this.index = -1;
        this._inorder(root);
    }
    private void _inorder(TreeNode root){
        if(root!=null) return;
        this._inorder(root.left);
        this.arr.add(root.val);
        this._inorder(root.right);
    }
    public int next(){
        return this.arr(++index);
    }
    public boolean hasNext(){
        return this.index+1<this.arr.size();
    }
}
```

###### 复杂度分析

- 时间复杂度：构造迭代器时间是$O(N)$，题目只要求我们分析两个函数的复杂性，但在实现类的时候，还要注意初始化对象所需的时间，在这种情况下，时间复杂度和节点数呈线性关系$O(1)$.
- 空间复杂度：$O(N)$， 因为构造了额外的数组空间存放所有节点，不符合题目$O(h)$

##### 受控递归

上面的方法不能暂停递归。但是，如果我们可以模拟中序遍历的受控递归，那么除了堆栈用于模拟递归的空间之外，实际上不需要使用任何其他空间。

因此，这种方法的本质上是**使用自定义的栈来模拟中序遍历**。也就是说，我们将**采用迭代的方式来模拟中序遍历**，而不是采用递归的方法；这样做的过程中，我们能够轻松的实现这两个函数的调用，而不是用其他额外的空间。

调用`next()`函数，返回栈顶元素，有两种可能性：

- 一个是栈顶部的节点是一个叶节点。这是最好的情况，因为我们什么都不用做，只需将节点从栈中弹出并返回其值。所以这是个常数时间的操作。
- 另一个情况是栈顶部的节点拥有右节点。我们不需要检查左节点，因为左节点已经添加到栈中了。栈顶元素要么没有左节点，要么左节点已经被处理了。如果栈顶部拥有右节点，那么我们需要对右节点上调用帮助函数。该时间复杂度取决于树的结构。


```java
class BSTIterator{
    Stack<TreeNode> stack;

    public BSTIterator(TreeNode root){
        this.stack=new Stack<>();
        this._inorder(root);
    }
    //要确保栈顶的元素包含了`next()`函数返回的元素
    private void _inorder(TreeNode root){
        while(root!=null){
            this.stack.push(root);
            root=root.left;
        }
    }
    public int next(){
        TreeNode cur = this.stack.pop();
        if(cur.right!=null){
            this._inorder(cur.right);
        }
        return cur.val;
    }
    public boolean hasNext(){
        return !this.stack.isEmpty();
    }
}
```

其实写法就是迭代中序遍历，上面有做笔记

```java
public inorder(TreeNode root){
    Stack<TreeNode> stack=new Stack<>();
    List<Integer> ans = new ArrayList<>();
    TreeNode cur=root;
    while(!stack.isEmpty() || cur!=null){
        while(cur!=null){
            stack.push(cur);
            cur=cur.left;
        }
        cur=stack.pop();
        ans.add(cur.val);
        cur=cur.right;
    }
    return ans;
}
```

###### 复杂度分析

- 时间复杂度: 
  - 遍历的复杂度是O(n)，调用n次`next()`才会遍历完，所以平均每次都是O(1)
  - `next()`包含了两个主要步骤。一个是从栈中弹出一个元素，它是下一个最小的元素。这是一个 $O(1)$ 的操作。然而，随后我们要调用帮助函数 `_inorder()` ，它需要递归的，将左节点添加到栈上，是线性时间的操作，最坏的情况下为 $O(N)$。但是我们只对含有右节点的节点进行调用，它也不会总是处理 N 个节点。只有当我们有一个倾斜的树，才会有 N 个节点。因此该操作的平均时间复杂度仍然是$O(1)$，符合问题中所要求的。

- 空间复杂度：$O(h)$

#### BST的合法性、搜索、插入和删除

> 要充分利用好二叉搜索树 节点之间大小的关系

##### 判断BST的合法性（节点之间的大小关系）

📕 不能想当然的单纯判断每个节点和左右子节点的关系，写成

```java
boolean isValidBST(TreeNode root){
    if(root==null) return true;
    if(root.left!=null && root.val<=root.left.val) return false;
    if(root.right!=null && root.val>=root.right.val) return false;
    
    return isValidBST(root.left) && isValidBST(root.right);
}
```

这样写遇到下面的情况会出错，因为每个节点应该要和**整个左子树和右子树所有节点比较**

```java
	10
  5    15
     6   10
```

这种情况，我们可以使用辅助函数，增加函数参数列表，在参数中携带额外信息

```java
boolean isValidBST(TreeNode root){
    if(root==null) return ture;
    return isValidBST(root, null, null);
}
boolean isValidBST(TreeNode root, TreeNode min, TreeNode max){
    if(root==null) return true;
    if(min!=null && root.val<=min.val) return false;
    if(max!=null && root.val>=max.val) return false;
    
    return isValidBST(root.left, min, root) 
        && isValidBST(root.right, root, max);
}
```

##### 查找一个数是否存在

```java
boolean isInBST(TreeNode root, int target){
    if(root==null) return false;
    if(root.val==target) return true;
    if(root.val>target) return isInBST(root.left, target);
    if(root.val<target) return isInBST(root.right, target);
}
```

##### 在BST中插入一个数

```java
TreeNode insertIntoBST(TreeNode root, int val){
    if(root=null)
        return new TreeNode(val);
    if(root.val>val) 
        root.left=insertIntoBST(root.left, val);
    if(root.val<val) 
        root.right=insertIntoBST(root.right, val);
    retuen root;
}
```

##### 在 BST 中删除一个数

**BST遍历基本框架**

```java
TreeNode deleteNode(TreeNode root, int key){
    if(root.val==key){
        //找到节点删除
    }
    if(root.val>val) 
        root.left=insertIntoBST(root.left, val);
    if(root.val<val) 
        root.right=insertIntoBST(root.right, val);
    retuen root;
}
```

当找到目标节点后进行删除时，有三种情况：

- 该节点没有左右子节点——直接删除
- 该节点只有一个子节点——将该子节点代替该节点
- 该节点有两个子节点——找出<u>右子树最小子节点/左子树中最大子节点</u>替换，然后删除最小子节点

```java
if(root.left==null) return root.right;
if(root.right==null) return root.left;
TreeNode _min = findMin(root.right);
root.val = _min.val;
root.right=delete(root.right, _min.val);
```

因此，完整代码如下：

```java
TreeNode deleteNode(TreeNode root, int key){
    if(root.val==key){
        if(root.left==null) return root.right;
		if(root.right==null) return root.left;
		TreeNode _min = findMin(root.right);
		root.val = _min.val;
		root.right=delete(root.right, _min.val);
    }
    if(root.val>val) 
        root.left=insertIntoBST(root.left, val);
    if(root.val<val) 
        root.right=insertIntoBST(root.right, val);
    
    retuen root;
}

TreeNode findMin(TreeNode node){
    while(node.left!=null) node=node.left;
    return node;
}
```