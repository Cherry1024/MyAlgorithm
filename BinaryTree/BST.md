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



