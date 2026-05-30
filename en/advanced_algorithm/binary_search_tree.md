# Binary Search Tree

## Definition

- The value in each node must be greater than (or equal to) any value stored in its left subtree.
- The value in each node must be less than (or equal to) any value stored in its right subtree.

## Applications

[validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> Validate a binary search tree

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func isValidBST(root *TreeNode) bool {
    return dfs(root).valid
}
type ResultType struct{
    max int
    min int
    valid bool
}
func dfs(root *TreeNode)(result ResultType){
    if root==nil{
        result.max=-1<<63
        result.min=1<<63-1
        result.valid=true
        return
    }

    left:=dfs(root.Left)
    right:=dfs(root.Right)

    // 1. Satisfy left max < root < right min && both left and right are valid
    if root.Val>left.max && root.Val<right.min && left.valid && right.valid {
        result.valid=true
    }
    // 2. Update the max and min values of the current node
    result.max=Max(Max(left.max,right.max),root.Val)
    result.min=Min(Min(left.min,right.min),root.Val)
    return
}
func Max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
func Min(a,b int)int{
    if a>b{
        return b
    }
    return a
}

```

[insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> Given the root node of a binary search tree (BST) and a value to insert into the tree, insert the value into the binary search tree. Return the root node of the binary search tree after insertion. It is guaranteed that the new value does not exist in the original binary search tree.

```go
func insertIntoBST(root *TreeNode, val int) *TreeNode {
    if root==nil{
        return &TreeNode{Val:val}
    }
    if root.Val<val{
        root.Right=insertIntoBST(root.Right,val)
    }else{
        root.Left=insertIntoBST(root.Left,val)
    }
    return root
}
```

[delete-node-in-a-bst](https://leetcode-cn.com/problems/delete-node-in-a-bst/)

> Given the root node root of a binary search tree and a value key, delete the node corresponding to key in the binary search tree while keeping the binary search tree property unchanged. Return a reference to the root node of the (possibly updated) binary search tree.

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func deleteNode(root *TreeNode, key int) *TreeNode {
    // Deleting a node falls into three cases:
    // 1. Only a left child: replace with the right
    // 2. Only a right child: replace with the left
    // 3. Has both left and right children: connect the left child to the leftmost node on the right
    if root ==nil{
        return root
    }
    if root.Val<key{
        root.Right=deleteNode(root.Right,key)
    }else if root.Val>key{
        root.Left=deleteNode(root.Left,key)
    }else if root.Val==key{
        if root.Left==nil{
            return root.Right
        }else if root.Right==nil{
            return root.Left
        }else{
            cur:=root.Right
            // Keep going left until reaching the last left node
            for cur.Left!=nil{
                cur=cur.Left
            }
            cur.Left=root.Left
            return root.Right
        }
    }
    return root
}
```

[balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

> Given a binary tree, determine whether it is height-balanced.

```go
type ResultType struct{
    height int
    valid bool
}
func isBalanced(root *TreeNode) bool {
    return dfs(root).valid
}
func dfs(root *TreeNode)(result ResultType){
    if root==nil{
        result.valid=true
        result.height=0
        return
    }
    left:=dfs(root.Left)
    right:=dfs(root.Right)
    // Satisfy all properties: binary search tree && balanced
    if left.valid&&right.valid&&abs(left.height,right.height)<=1{
        result.valid=true
    }
    result.height=Max(left.height,right.height)+1
    return
}
func abs(a,b int)int{
    if a>b{
        return a-b
    }
    return b-a
}
func Max(a,b int)int{
    if a>b{
        return a
    }
    return b
}

```

## Exercises

- [ ] [validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)
- [ ] [insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
- [ ] [delete-node-in-a-bst](https://leetcode-cn.com/problems/delete-node-in-a-bst/)
- [ ] [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)
