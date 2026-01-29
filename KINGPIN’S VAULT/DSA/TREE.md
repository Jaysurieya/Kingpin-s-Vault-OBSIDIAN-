==In JAVA Queue is not available directly so we need to use linked list instead of queue

LC 94: [inorder traversal]
optimal using stack: 
```
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        List<Integer> list = new ArrayList<>();
        if(root == null) return list;
        while(!(stack.empty())){
            if((stack.peek()).left==null){
                TreeNode temp = stack.pop();
                list.add(temp.val);
                if(temp.right!=null){
                    stack.push(temp.right);
                }
            }else{
                TreeNode t = (stack.peek()).left;
                (stack.peek()).left = null;
                stack.push(t);
            }
        }
        return list;
    }
}
```

LC 144: [preorder traversal]
using recurrsion:
```
class Solution {
    private List<Integer> list = new ArrayList<>();
    public List<Integer> preorderTraversal(TreeNode root) {
        pot(root);
        return list;
    }
    public void pot(TreeNode current){
        if(current==null) return;
        list.add(current.val);
        pot(current.left);
        pot(current.right);
    }
}
```

LC 222: [count the no of nodes in a tree]
using recurrsion:
```
class Solution {
    private int count = 0;
    public int countNodes(TreeNode root) {
        count(root);
        return count;  
    }
    public void count(TreeNode current){
        if(current==null) return ;
        count++;
        count(current.left);
        count(current.right);
    }
}
```
or more easy approach:
```
class Solution {
    public int countNodes(TreeNode current) {
        if(current==null) return 0;
        return 1 + countNodes(current.left) + countNodes(current.right);
    }
}
```

gfg - ### Count Leaves in Binary Tree problem :
```
class Solution {
    int c = 0;
    int countLeaves(Node node) {
        count(node);
        return c;
    }
    void count(Node current){
        if(current==null) return;
        if(current.left==null && current.right==null){
            c++;
            return;
        }
        count(current.left);
        count(current.right);
    }
}
```
or
```
class Solution {
    int countNonLeafNodes(Node root) {
        if(root == null) return 0;
        if(root.left==null && root.right==null) return 1;
        return  countNonLeafNodes(root.left)+countNonLeafNodes(root.right);
    }
}
```
gfg - count parents:
```
class Solution {
    int countLeaves(Node root) {
        if(root == null) return 0;
        if(root.left==null && root.right==null) return 1;
        return  countLeaves(root.left)+countLeaves(root.right);
    }
}
```

gfg - sum of binary tree:
```

```

LC 543: [Diameter of a binary tree]
optimal:
```
class Solution {
    public int diameter = 0;
    public int diameterOfBinaryTree(TreeNode root) {
        height(root);
        return diameter;
    }
    public int height(TreeNode current){
        if(current==null) return 0;
  
        int lh = height(current.left);
        int rh = height(current.right);
  
        if((lh+rh)>diameter) diameter = (lh+rh);
        return Math.max(lh,rh)+1;
    }
}
```

◽️ A definition of diameter

The diameter of a binary tree is the length of the longest path between any two nodes in a tree. This path may or may not pass through the root.

---

Simply, by comparing the length of paths from end to end, we can obtain the longest path. Using DFS, we can increment the length by `1` as we backtrack, which should allow us to find the longest path.

Let's move down to node `4` from root node.

```python
     n1
     / \
   n2  n3
   / \
#n4  n5
0/ \0

# is current node
n is node
```

From node `4`, there is no children, so length should be `0`. Let's go back to node `2`.

But before that, let's calculate `current max length`. Current max length should be `current result` or `left + right`.

```python
res = max(res, l + r)
res = max(0, 0 + 0)
= 0
```

After that, we should add `1` to longer length of path between left or right to keep the longest length one way path, **because the diameter of a binary tree is the length of the longest path between any two nodes in a tree. We will have one node from subtree from child and have the other node when we go back to parent node.**

Add `1` to longer length between left or right. In this case both side are `0`, so current length should be

```sql
1 + max(l, r)
= 1 + max(0, 0)
= 1 (= between node 2 and node 4)
```

We do the same thing for right side of node `2`

```python
     n1
     / \
  #n2  n3
  1/ \
 n4  n5
0/ \00/\0
```

```python
     n1
     / \
   n2  n3
  1/ \
 n4  n5#
0/ \00/\0

res = max(res, l + r)
res = max(0, 0 + 0)
= 0
```

Let's go back to node `2` from node `5`

```python
     n1
     / \
   n2  n3
  1/ \1
 n4  n5#
0/ \00/\0

1 + max(l, r)
= 1 + max(0, 0)
= 1(= between node 2 and node 5)

     n1
     / \
  #n2  n3
  1/ \1
 n4  n5
0/ \00/\0
```

Let's move back to root node from node `2`. But before that let's calculate max length.

```python
     n1
     / \
  #n2  n3
  1/ \1
 n4  n5
0/ \00/\0

res = max(res, l + r)
res = max(0, 1 + 1)
= 2 (4 → 2 → 5 or 5 → 2 → 4)
```

```python
    #n1
    2/ \
   n2  n3
  1/ \1
 n4  n5
0/ \00/\0

1 + max(l, r)
= 1 + max(1, 1)
= 2 (=between node 1 and node 4 or 5)
```

Let's go right side of node `1`.

```python
     n1
    2/ \
   n2  #n3
  1/ \1 0/\0
 n4  n5
0/ \00/\0

res = max(res, l + r)
res = max(2, 0 + 0)
= 2 (4 → 2 → 5 or 5 → 2 → 4)
```

Let's go back to node `1`.

```python
    #n1
   2 / \ 1
   n2  n3
  1/ \1 0/\0
 n4  n5
0/ \00/\0

1 + max(l, r)
= 1 + max(0, 0)
= 1 (=between node 1 and node 3)
```

In the end, calculate max length at root node.

```python
res = max(res, l + r)
res = max(2, 2 + 1)
= 3 

4 → 2 → 1 → 3 or
5 → 2 → 1 → 3 or
3 → 1 → 2 → 4 or
3 → 1 → 2 → 5
```


LC 100:
optimal:
```
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if(p==null && q==null) return true;
        if((p==null&&q!=null) || (q==null&&p!=null)) return false;
        if(p.val!=q.val) return false;
        boolean x = isSameTree(p.left,q.left);
        boolean y = isSameTree(p.right,q.right);
        return (x && y);
    }
}
```

check mirror tree - gfg:
```

```

LC - 101
optimal: [just make small changes in the same tree logic] 
```
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return (isSameTree(root.left,root.right));
    }
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if(p==null && q==null) return true;
        if((p==null&&q!=null) || (q==null&&p!=null)) return false;
        if(p.val!=q.val) return false;
        boolean x = isSameTree(p.left,q.right);
        boolean y = isSameTree(p.right,q.left);
        return (x && y);
    }
}
```

sum tree - gfg
bst construction
deletion in bst
kth largest element in bst
LC 112:
```

```
LC 129
level of btree
left view and right view of btree
vertical view of btree
## Level order traversal

gfg - ### Levelorder traversal of a BST [flat output ---> [1,2,3,4,5]]:

==queue = linked list 
here push = add,
pop = poll==

```
class Solution {
    // Function to return the level order traversal of a BST.
    static ArrayList<Integer> levelOrder(Node node) {
        Queue<Node> queue = new LinkedList<>();
        queue.add(node);
        ArrayList<Integer> list = new ArrayList<>();
        while(!queue.isEmpty()){
            Node x = queue.poll();
            list.add(x.data);
            if(x.left!=null) queue.add(x.left);
            if(x.right!=null) queue.add(x.right);
        }
        return list;
    }
    
}
```

LC - 102:
approach better: [i dont know its optimal or not]	
```
class Solution {
    List<List<Integer>> list = new ArrayList<>();
    Queue<TreeNode> queue =  new LinkedList<>();
    public List<List<Integer>> levelOrder(TreeNode root) {
        if(root==null) return list;
        queue.add(root);
        l_order(1);
        return list;
    }
    public void l_order(int count){
        while(!(queue.isEmpty())){
            List<Integer> l = new ArrayList<>();
            int c =0;
            while(count!=0){
                TreeNode x = queue.poll();
                count--;
                l.add(x.val);
                if(x.left!=null){
                    queue.add(x.left);
                    c++;
                } 
                if(x.right!=null){
                    queue.add(x.right);
                    c++;
                } 
            }
            count = c;
            list.add(l);
        }
    }
}
```

LC - 103 : [zigzag order of btree]
```

```