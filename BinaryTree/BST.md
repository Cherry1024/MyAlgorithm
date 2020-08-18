### 二叉树的遍历

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

### 二叉树的递归

#### 自顶向下

首先访问节点计算一些值，并在递归调用函数时将这些值传递到子节点

```java
return specific value for null node
update the answer if needed                      // anwer <-- params
left_ans = top_down(root.left, left_params)		// left_params <-- root.val, params
right_ans = top_down(root.right, right_params) // right_params <-- root.val, params
return the answer if needed               // answer <-- left_ans, right_ans
```

> 求二叉树的最大深度

```java
int ans = 0;
void max_depth(TreeNode root, int depth){
    if(root==null) return;
    //到达根节点
    if(root.left==null && root.right==null){
        ans=Math.max(ans,depth);
    }
    max_depth(root.left,depth+1);
    max_depth(root.right,depth+1);
}
```

#### 自底向上

在每个递归层次上，我们首先对所有子节点递归地调用函数，然后根据返回值和根节点本身的值得到答案。

```java
return specific value for null node
left_ans = bottom_up(root.left)	 // call function recursively for left child
right_ans = bottom_up(root.right) // call function recursively for right child
return answers  // answer <-- left_ans, right_ans, root.val
```

> 求二叉树的最大深度

```java
public int maximum_depth(TreeNode root) {
	if (root == null) {
		return 0;    // return 0 for null node
	}
	int left_depth = maximum_depth(root.left);
	int right_depth = maximum_depth(root.right);
	return Math.max(left_depth, right_depth) + 1; // return depth of the subtree rooted at root
```

#### 对称二叉树

>递归的难点在于：找到可以递归的点 为什么很多人觉得递归一看就会，一写就废。 或者说是自己写无法写出来，关键就是你对递归理解的深不深。
>
>对于此题： 递归的点怎么找？从拿到题的第一时间开始，思路如下：
>
>1.怎么判断一棵树是不是对称二叉树？ 答案：如果所给根节点，为空，那么是对称。如果不为空的话，当他的左子树与右子树对称时，他对称
>
>2.那么怎么知道左子树与右子树对不对称呢？在这我直接叫为左树和右树 答案：如果左树的左孩子与右树的右孩子对称，左树的右孩子与右树的左孩子对称，那么这个左树和右树就对称。
>
>当你思考到这里的时候，递归点已经出现了： 递归点：我在尝试判断左树与右树对称的条件时，发现其跟两树的孩子的对称情况有关系。
>
>想到这里，你不必有太多疑问，上手去按思路写代码，函数A（左树，右树）功能是返回是否对称
>
>def 函数A（左树，右树）： 左树节点值等于右树节点值 且 函数A（左树的左子树，右树的右子树），函数A（左树的右子树，右树的左子树）均为真 才返回真
>
>实现完毕。。。
>
>写着写着。。。你就发现你写出来了。。。。。。

```java
//递归模式
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if(root==null) return true;
        if(check(root.left,root.right)) return true;
        else return false;
    }
    public boolean check(TreeNode l, TreeNode r){
        if(l==null && r==null) return true;
        if(l==null || r==null) return false;
        if(l.val!=r.val) return false;
        return check(l.left,r.right) && check(l.right,r.left);
    }
}
```

```java
//迭代模式
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if(root==null) return true;
        if(root.left==null && root.right==null) return true;
        //Queue<TreeNode> queue = new Deque<>(); //不能用ArrayDeque，会报错，因为其元素不能为null！！！
        Queue<TreeNode> queue = new LinkedList<>(); 
        queue.offer(root.left);
        queue.offer(root.right);
        while(!queue.isEmpty()){
            TreeNode l_cur = queue.poll();
            TreeNode r_cur = queue.poll();
            if(l_cur==null && r_cur==null) continue; //不能直接返回true
            if(l_cur==null || r_cur==null) return false;
            if(l_cur.val!=r_cur.val) return false;
            queue.offer(l_cur.left);
            queue.offer(r_cur.right);
            queue.offer(l_cur.right);
            queue.offer(r_cur.left);
        }
        return true;
    }
}
```

**报java.lang.NullPointerException的原因:**

> - 类未初始化，比如接口类型的对象没有用具体的类初始化 // List list; 会报错
> - 当一个对象的值为空时，你没有判断为空的情况  if(rb!=null&&!"".equals(rb.trim()))

#### 路经总和

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if(root==null) return false;
        int val = 0;
        if(cal(root,val,sum)) return true;
        else return false;
        
    }
    public boolean cal(TreeNode root,int val, int sum){
        if(root==null) return false;
        //达到叶子节点
        if(val==sum && root.left==null && root.right ==null) 
            return true;
     
        return cal(root.left, val+root.val, sum) || cal(root.right, val+root.val, sum); 
    }
}
//这样的写法结果会出错
```

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if(root==null) return false;
        int val = 0;
        if(cal(root,val,sum)) return true;
        else return false;
        
    }
    public boolean cal(TreeNode root,int val, int sum){
        if(root==null) return false;
        //达到叶子节点
        val = val+root.val;
        if(val==sum && root.left==null && root.right ==null) 
            return true;
     
        return cal(root.left, val, sum) || cal(root.right, val, sum); 
    }
}
```

### 总结

#### 从中序与后序遍历序列构造二叉树

##### 树的还原过程

1. 根据后序数组（最后一位）获得根节点

2. 根据根节点的值找到中序数组的索引      ***！！！！HashMap存放！！！！*** 

3. 根据索引可以将中序数组分为左子树和右子树   int index

4. 根据索引确定左子树和右子树在中序(inorderStart, inorderEnd)和后序(postorderStart, int postorderEnd)数组中的左右边界位置

5. 递归构造左子树和右子树

6. 返回根节点结束

   <img src="https://i.loli.net/2020/08/17/d3UbLCR7vxAJikV.png" alt="image-20200817150844936" style="zoom: 80%;" />

```java
class Solution {
    //将节点的值和索引存在HashMap里面
    //！！！！看题解学到的！之前自己还想着每次找都遍历一遍，还是太年轻了
    HashMap<Integer, Integer> inorderMap = new HashMap<>();
    int[] post;
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        for(int i=0; i<inorder.length;i++){            
            inorderMap.put(inorder[i],i); 
        }
        post=postorder;
        TreeNode node=buildTree(0, inorder.length-1, 0, postorder.length-1);
        return node;
    }
    public TreeNode buildTree(int inorderStart, int inorderEnd, int postorderStart, int postorderEnd){
        if(inorderStart>inorderEnd || postorderEnd<postorderStart) return null;
        int root=post[postorderEnd];
        int index=inorderMap.get(root);
        TreeNode node = new TreeNode(root);
        node.left=buildTree(inorderStart,index-1,postorderStart,postorderStart+index-inorderStart-1);
        node.right=buildTree(index+1,inorderEnd,postorderStart+index-inorderStart,postorderEnd-1);
        return node;
    }
}
```

#### 填充每个节点的下一个右侧节点指针

**leetcode[116](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)**

📕 重点：常数空间。 所以：排除用队列BFS的思路。用了队列就是N的空间复杂度。

📕 要抓住关键：每个 node 左子树的 next , 就是 node 的右子树；每个 node 右子树的 next, 就是 node next 的 左子树

```java
class Solution {
    public Node connect(Node root) {
        dfs(root, null);
        return root;
    }
    public void dfs(Node root, Node next){
        if(root!=null) {
            root.next=next;
            dfs(root.left, root.right);
            if(root.next==null) dfs(root.right,null);
            else dfs(root.right, root.next.left);
        }
    }
}
```

#### !!!填充每个节点的下一个右侧节点指针 

📕📕📕**leetcode[117](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/)**   这道题要好好理解！！琢磨了我一下午，思路其实并不难，但在实现上有很多细节我考虑不到，尤其是发现自己对于指针、Java对象赋值这一块真的理解不够深入！！！

##### BFS

很容易想到用队列存放每一层的节点，通过`pre`指针把栈里的元素一个一个接起来。但是空间复杂度$O(n)$不满足题目要求：`You may only use constant extra space.`

```java
public Node connect(Node root) {
    if (root == null) {
        return root;
    }
    Queue<Node> queue = new LinkedList<Node>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        int size = queue.size();
        Node pre = null;
        for (int i = 0; i < size; i++) {
            Node cur = queue.poll();
            if (i > 0) {
                pre.next = cur;
            }
            pre = cur;
            if (cur.left != null) {
                queue.offer(cur.left);
            }
            if (cur.right != null) {
                queue.offer(cur.right);
            }

        }
    }
    return root;
}
```

##### BFS的改进——dummy指针

利用BFS的思想，我们利用 `pre`指针，然后一个一个取节点，把它连起来。解法一为什么没有像解法二那样考虑当前节点为`null`呢？因为我们没有添加为`null`的节点，就是下边的代码的作用。

```java
if (cur.left != null) {
    queue.offer(cur.left);
}
if (cur.right != null) {
    queue.offer(cur.right);
}
```

所以这里是一样的，如果当前节点为`null`不处理就可以了。

第二个问题，怎么得到每次的开头的节点呢？我们用一个`dummy`指针，当连接第一个节点的时候，就将`dummy`指针指向他。此外，之前用的`pre`指针，把它当成`tail`指针可能会更好理解。如下图所示：

<img src="https://i.loli.net/2020/08/18/cBHWCYgAjn4DvsR.png" alt="image-20200818213439848" style="zoom:67%;" />

`cur`指针利用`next`不停的遍历当前层。

如果 `cur` 的孩子不为`null`就将它接到 `tail` 后边，然后更新`tail`。

当 `cur` 为 `null` 的时候，再利用 `dummy` 指针得到新的一层的开始节点。

`dummy` 指针在链表中经常用到，他只是为了处理头结点的情况，它并不属于当前链表。

代码实现如下：

```java
class Solution {
    public Node connect(Node root) {
        Node cur = root;
        //遍历每一层
        while(cur!=null){
            Node dummpy = new Node();
            Node tail = dummpy;
            //cur层的遍历
            while(cur!=null){
                if(cur.left!=null){
                    tail.next=cur.left;
                    tail=tail.next;
                }
                if(cur.right!=null){
                    tail.next=cur.right;
                    tail=tail.next;
                }
                cur=cur.next;  //继续cur层右边的节点
            }
            cur=dummpy.next;  //下一层
        }
        return root;
    }
}
```



