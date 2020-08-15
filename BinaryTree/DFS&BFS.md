### BFS 的使用场景总结：层序遍历、最短路径问题

摘自：[leetcode](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/solution/bfs-de-shi-yong-chang-jing-zong-jie-ceng-xu-bian-l/)

#### DFS 与 BFS 的特点比较

```java
// DFS遍历使用递归：
void dfs(TreeNode root){
    if (root==null) return;
    dfs(root.left);
    dfs(root.right);
}

//BFS遍历使用队列：
void bfs(TreeNode root){
    Queue<TreeNode> queue = new ArrayDeque<>();
    queue.add(root); //队列尾部插入root
    while(!queue.isEmpty()){
        //取出队列头部元素，并从队列中移除； 队列为空，返回null
        TreeNode cur = queue.poll(); 
        if(cur.left!=null)  queue.add(cur.left);
        if(cur.right!=null) queue.add(cur.right);  
    }
}
```

> Deque接口集成自Queue，支持同时从两端添加。ArrayDeque是Deque接口的一种具体实现， 底部是使用数组存储元素，同时还使用了两个索引来表征当前数组的状态，分别是 head 和 tail。head 是头部元素的索引，但注意 tail *不是尾部元素的索引，而是尾部元素的下一位*，即下一个将要被加入的元素的索引。
>
> ArrayDeque 没有容量限制，可根据需求自动进行扩容。ArrayDeque 可以作为栈来使用，效率要高于 Stack；ArrayDeque 也可以作为队列来使用，效率相较于基于双向链表的 LinkedList 也要更好一些。ArrayDeque 不支持为 null 的元素。
>
> [参考资料](https://blog.jrwang.me/2016/java-collections-deque-arraydeque/)

#### BFS 的适用场景

层序遍历、最短路径

#### 如何用 BFS 进行层序遍历

**层序遍历**要求输出每一层，返回二维数组，但BFS遍历结果是一个一维数组，无法区分每一层

<img src="https://i.loli.net/2020/08/15/2tfDbRwKQm3JTXN.png" alt="image-20200815181939824" style="zoom:50%;" />

此时队列中的节点分别来自不同层，无法区分队列中的节点来自哪一层

因此稍微修改一下代码——在每一层遍历开始之前，先记录队列中的节点数量n（即这一层的节点数量），然后一次性处理完这一层的n个节点

```java
void dfs(TreeNode root){
    Queue<TreeNode> queue = ArrayDeque<>();
    queue.add(root);
    //在while循环的每一轮，都是将当前层的所有节点出队列，再将下一层的所有节点入队列
    while(!queue.isEmpty()){
        int n = queue.size();
        for(int i=0; i<n; i++){
            TreeNode cur = queue.poll();
            if(cur.left!=null) queue.add(cur.left);
            if(cur.right!=null) queue.add(cur.right);
        }

    }
}
```

因此层序遍历的完整实现：

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> ans = new ArrayList<>();
    Queue<TreeNode> queue = new ArrayDeque<>();
    if(root!=null) queue.add
```



#### 如何用 BFS 求解最短路径问题