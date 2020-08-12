- 二叉搜索树是特殊的二叉树——根节点的值≥左节点的值，≤右节点的值

- 对于前序遍历，可以按顺序遍历所有数据

- 对于后序遍历，

   - 如果要删除树的节点，则应该按照后序遍历——先删除左节点、右节点再删除根节点

   - 常用于数学表达：

     <img src="https://i.loli.net/2020/08/12/FaGsnIDPudg9m3w.png" alt="image-20200812114313432" style="zoom:50%;" />

     可以用中序遍历将该表达式还原，但是很难用程序handle因为要考虑操作符优先级

     但用后序遍历就很方便——**当遇到操作符时，从栈中pop两个元素计算，再将结果push回栈中**

#### 144.Binary Tree Preorder Traversal (medium)

这题用迭代的思想的来实现树的前序遍历。

这是开始第一道题，都忘光了数据结构的定义初始化了😂

树遍历的迭代实现（除层次遍历使用的是队列），一般来说都是使用栈，所以要定义栈存放节点

**核心思想为：**

1. 每拿到一个 节点 就把它保存在 栈 中
2. 继续对这个节点的 左子树 重复 过程1，直到左子树为 空
3. 因为保存在 栈 中的节点都遍历了 左子树 但是没有遍历 右子树，所以对栈中节点 出栈 并对它的 右子树 重复 过程1
4. 直到遍历完所有节点

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        
        while(cur!=null || !stack.isEmpty()){
            while(cur!=null){
                stack.push(cur);
                ans.add(cur.val);
                cur=cur.left;
            }
            cur=stack.pop();
            cur=cur.right;
        }
        return ans;
    }
}

```

#### 145.Binary Tree Inorder Traversal (medium)

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        List<Integer> ans = new ArrayList<>();
        TreeNode cur = root;
        while(cur!=null || !stack.empty()){
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
}
```

#### 146.Binary Tree postorder Traversal (medium)

递归的本质就是压栈，了解递归本质后就完全可以按照递归的思路来迭代。
怎么压，压什么？压的当然是待执行的内容，后面的语句先进栈，所以进栈顺序就决定了前中后序。
我们需要一个标志区分每个递归调用栈，这里使用null来表示

```java
// 代码可以迁移到前后中序！
public List<Integer> postorderTraversal(TreeNode root) {
    if(root==null) return Collections.emptyList();
    Stack<TreeNode> stack = new Stack<>(); //调用栈
    List<Integer> ans = new ArrayList<>(); //保存结果
    stack.push(root); //push root节点
    while(!stack.empty()){
        TreeNode cur=stack.pop(); //访问过的节点弹出
        if(cur!=null){
            stack.push(cur); //在右节点之前重新插入该节点，以便在最后处理（访问值）
            stack.push(null); //null跟随cur插入，标识已经访问过，还没有被处理
            if(cur.right!=null) stack.push(cur.right);
            if(cur.left!=null) stack.push(cur.left);
        }
        else{//空节点表示之前已经访问过了，现在需要处理除了递归之外的内容
             //栈顶是null之前压栈的一个节点，也就是上面stack.push(cur)中的那个cur
             //stack.pop() 处理完了，第二次弹出节点（彻底从栈中移除）
            ans.add(stack.pop().val);
        }
    }
    return ans;
}
```



