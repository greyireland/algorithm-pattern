# Binary Tree

## Key Concepts

### Binary Tree Traversal

**Preorder traversal**: **visit the root node first**, then preorder-traverse the left subtree, then preorder-traverse the right subtree
**Inorder traversal**: first inorder-traverse the left subtree, **then visit the root node**, then inorder-traverse the right subtree
**Postorder traversal**: first postorder-traverse the left subtree, then postorder-traverse the right subtree, **then visit the root node**

Notes

- The order in which the root is visited determines which traversal it is
- The left subtree always takes priority over the right subtree

#### Preorder (Recursive)

```go
func preorderTraversal(root *TreeNode)  {
    if root==nil{
        return
    }
    // visit the root first, then left and right
    fmt.Println(root.Val)
    preorderTraversal(root.Left)
    preorderTraversal(root.Right)
}
```

#### Preorder (Iterative)

```go
// V3: iterative traversal
func preorderTraversal(root *TreeNode) []int {
    // iterative
    if root == nil{
        return nil
    }
    result:=make([]int,0)
    stack:=make([]*TreeNode,0)

    for root!=nil || len(stack)!=0{
        for root !=nil{
            // preorder, so save the result first
            result=append(result,root.Val)
            stack=append(stack,root)
            root=root.Left
        }
        // pop
        node:=stack[len(stack)-1]
        stack=stack[:len(stack)-1]
        root=node.Right
    }
    return result
}
```

#### Inorder (Iterative)

```go
// Idea: use a stack to save already-visited elements so we can backtrack
func inorderTraversal(root *TreeNode) []int {
    result := make([]int, 0)
    if root == nil {
        return result
    }
    stack := make([]*TreeNode, 0)
    for len(stack) > 0 || root != nil {
        for root != nil {
            stack = append(stack, root)
            root = root.Left // keep going left
        }
        // pop
        val := stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        result = append(result, val.Val)
        root = val.Right
    }
    return result
}
```

#### Postorder (Iterative)

```go
func postorderTraversal(root *TreeNode) []int {
	// use lastVisit to mark whether the right child has already been popped
	if root == nil {
		return nil
	}
	result := make([]int, 0)
	stack := make([]*TreeNode, 0)
	var lastVisit *TreeNode
	for root != nil || len(stack) != 0 {
		for root != nil {
			stack = append(stack, root)
			root = root.Left
		}
		// just peek here, do not pop yet
		node:= stack[len(stack)-1]
		// the root node must be popped only after the right node is popped
		if node.Right == nil || node.Right == lastVisit {
			stack = stack[:len(stack)-1] // pop
			result = append(result, node.Val)
			// mark that the current node has been popped
			lastVisit = node
		} else {
			root = node.Right
		}
	}
	return result
}
```

Notes

- The core idea: the root node must be popped only after the right node is popped

#### DFS Depth-First Search — Top-Down

```go
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

func preorderTraversal(root *TreeNode) []int {
    result := make([]int, 0)
    dfs(root, &result)
    return result
}

// V1: depth-first traversal, passing a pointer to the result into the function
func dfs(root *TreeNode, result *[]int) {
    if root == nil {
        return
    }
    *result = append(*result, root.Val)
    dfs(root.Left, result)
    dfs(root.Right, result)
}
```

#### DFS Depth-First Search — Bottom-Up (Divide and Conquer)

```go
// V2: traversal using divide and conquer
func preorderTraversal(root *TreeNode) []int {
    result := divideAndConquer(root)
    return result
}
func divideAndConquer(root *TreeNode) []int {
    result := make([]int, 0)
    // return condition (null & leaf)
    if root == nil {
        return result
    }
    // Divide
    left := divideAndConquer(root.Left)
    right := divideAndConquer(root.Right)
    // Conquer (merge results)
    result = append(result, root.Val)
    result = append(result, left...)
    result = append(result, right...)
    return result
}
```

Notes:

> The difference between DFS (top-down) and divide and conquer: the former generally passes the final result through a pointer parameter, while the latter generally returns results from recursion and merges them at the end

#### BFS Level-Order Traversal

```go
func levelOrder(root *TreeNode) [][]int {
    // use the length of the previous level to determine the elements of the next level
    result := make([][]int, 0)
    if root == nil {
        return result
    }
    queue := make([]*TreeNode, 0)
    queue = append(queue, root)
    for len(queue) > 0 {
        list := make([]int, 0)
        // why take the length?
        // record how many elements are in the current level (traverse the current level, then add the next level)
        l := len(queue)
        for i := 0; i < l; i++ {
            // dequeue
            level := queue[0]
            queue = queue[1:]
            list = append(list, level.Val)
            if level.Left != nil {
                queue = append(queue, level.Left)
            }
            if level.Right != nil {
                queue = append(queue, level.Right)
            }
        }
        result = append(result, list)
    }
    return result
}
```

### Applications of Divide and Conquer

Process the parts separately first, then merge the results

Applicable scenarios

- Quicksort
- Merge sort
- Binary tree related problems

Divide and conquer template

- Recursion return condition
- Process each part
- Merge results

```go
func traversal(root *TreeNode) ResultType  {
    // nil or leaf
    if root == nil {
        // do something and return
    }

    // Divide
    ResultType left = traversal(root.Left)
    ResultType right = traversal(root.Right)

    // Conquer
    ResultType result = Merge from left and right

    return result
}
```

#### Typical Example

```go
// V2: traverse the binary tree using divide and conquer
func preorderTraversal(root *TreeNode) []int {
    result := divideAndConquer(root)
    return result
}
func divideAndConquer(root *TreeNode) []int {
    result := make([]int, 0)
    // return condition (null & leaf)
    if root == nil {
        return result
    }
    // Divide
    left := divideAndConquer(root.Left)
    right := divideAndConquer(root.Right)
    // Conquer (merge results)
    result = append(result, root.Val)
    result = append(result, left...)
    result = append(result, right...)
    return result
}
```

#### Merge Sort

```go
func MergeSort(nums []int) []int {
    return mergeSort(nums)
}
func mergeSort(nums []int) []int {
    if len(nums) <= 1 {
        return nums
    }
    // divide and conquer: divide into two halves
    mid := len(nums) / 2
    left := mergeSort(nums[:mid])
    right := mergeSort(nums[mid:])
    // merge the two halves
    result := merge(left, right)
    return result
}
func merge(left, right []int) (result []int) {
    // cursors for merging the two arrays
    l := 0
    r := 0
    // be careful not to go out of bounds
    for l < len(left) && r < len(right) {
        // merge whichever is smaller
        if left[l] > right[r] {
            result = append(result, right[r])
            r++
        } else {
            result = append(result, left[l])
            l++
        }
    }
    // merge the remaining parts
    result = append(result, left[l:]...)
    result = append(result, right[r:]...)
    return
}
```

Notes

> Recursion needs to return results for merging

#### Quicksort

```go
func QuickSort(nums []int) []int {
	// Idea: split an array into left and right halves where the left half is less than the right half; like divide and conquer but without a merge step
	quickSort(nums, 0, len(nums)-1)
	return nums

}
// in-place swap, so we pass in the swap indices
func quickSort(nums []int, start, end int) {
	if start < end {
        // divide and conquer: divide
		pivot := partition(nums, start, end)
		quickSort(nums, start, pivot-1)
		quickSort(nums, pivot+1, end)
	}
}
// partition
func partition(nums []int, start, end int) int {
	p := nums[end]
	i := start
	for j := start; j < end; j++ {
		if nums[j] < p {
			swap(nums, i, j)
			i++
		}
	}
    // swap the middle value with the pivot used for comparison
	swap(nums, i, end)
	return i
}
func swap(nums []int, i, j int) {
	t := nums[i]
	nums[i] = nums[j]
	nums[j] = t
}
```

Notes:

> Since quicksort swaps in place, there is no merge step
> The indices passed in must be valid indices (e.g. 0, length-1, etc.); going out of bounds may cause a crash

Common problem examples

#### maximum-depth-of-binary-tree

[maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

> Given a binary tree, find its maximum depth.

Idea: divide and conquer

```go
func maxDepth(root *TreeNode) int {
    // handle the return condition
    if root == nil {
        return 0
    }
    // divide: compute the left and right subtrees separately
    left := maxDepth(root.Left)
    right := maxDepth(root.Right)

    // conquer: merge the results of the left and right subtrees
    if left > right {
        return left + 1
    }
    return right + 1
}
```

#### balanced-binary-tree

[balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

> Given a binary tree, determine whether it is a height-balanced binary tree.

Idea: divide and conquer — the left side is balanced && the right side is balanced && the height difference between left and right <= 1.
Since we need to return both whether it is balanced and the height, we either return two values or combine the two values,
so we use -1 to indicate not balanced and >0 to indicate the tree height (ambiguity: one variable carrying two meanings).

```go
func isBalanced(root *TreeNode) bool {
    if maxDepth(root) == -1 {
        return false
    }
    return true
}
func maxDepth(root *TreeNode) int {
    // check
    if root == nil {
        return 0
    }
    left := maxDepth(root.Left)
    right := maxDepth(root.Right)

    // why return -1? (the variable carries two meanings)
    if left == -1 || right == -1 || left-right > 1 || right-left > 1 {
        return -1
    }
    if left > right {
        return left + 1
    }
    return right + 1
}
```

Note

> In general engineering practice, the result is returned through two variables; it is not recommended to use one variable to represent two meanings

#### binary-tree-maximum-path-sum

[binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

> Given a **non-empty** binary tree, return its maximum path sum.

Idea: divide and conquer, split into three cases: the left subtree has the largest maximum path sum, the right subtree has the largest maximum path sum, or both subtrees plus the root give the largest sum. We need to keep two variables: one to save the subtree's maximum path sum, and one to save the sum of both sides plus the root, then compare these two variables and pick the maximum.

```go
type ResultType struct {
    SinglePath int // save the maximum value for a single side
    MaxPath int // save the maximum value (a single side, or two single sides + the root value)
}
func maxPathSum(root *TreeNode) int {
    result := helper(root)
    return result.MaxPath
}
func helper(root *TreeNode) ResultType {
    // check
    if root == nil {
        return ResultType{
            SinglePath: 0,
            MaxPath: -(1 << 31),
        }
    }
    // Divide
    left := helper(root.Left)
    right := helper(root.Right)

    // Conquer
    result := ResultType{}
    // compute the maximum value for a single side
    if left.SinglePath > right.SinglePath {
        result.SinglePath = max(left.SinglePath + root.Val, 0)
    } else {
        result.SinglePath = max(right.SinglePath + root.Val, 0)
    }
    // compute the maximum value of both sides plus the root
    maxPath := max(right.MaxPath, left.MaxPath)
    result.MaxPath = max(maxPath,left.SinglePath+right.SinglePath+root.Val)
    return result
}
func max(a,b int) int {
    if a > b {
        return a
    }
    return b
}
```

#### lowest-common-ancestor-of-a-binary-tree

[lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> Given a binary tree, find the lowest common ancestor of two specified nodes in the tree.

Idea: divide and conquer — if there is a common ancestor in the left subtree or a common ancestor in the right subtree, return that subtree's ancestor; otherwise return the root node

```go
func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
    // check
    if root == nil {
        return root
    }
    // if equal, just return the root node directly
    if root == p || root == q {
        return root
    }
    // Divide
    left := lowestCommonAncestor(root.Left, p, q)
    right := lowestCommonAncestor(root.Right, p, q)


    // Conquer
    // if neither side is nil, then the root node is the ancestor
    if left != nil && right != nil {
        return root
    }
    if left != nil {
        return left
    }
    if right != nil {
        return right
    }
    return nil
}
```

### BFS Level-Order Applications

#### binary-tree-level-order-traversal

[binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

> Given a binary tree, return the node values obtained by **level-order traversal**. (i.e. visit all nodes level by level, from left to right)

Idea: use a queue to record the elements of one level, then scan this level's elements and add the next level's elements to the queue (each number goes in and comes out once, so the complexity is O(logN))

```go
func levelOrder(root *TreeNode) [][]int {
	result := make([][]int, 0)
	if root == nil {
		return result
	}
	queue := make([]*TreeNode, 0)
	queue = append(queue, root)
	for len(queue) > 0 {
		list := make([]int, 0)
        // why take the length?
        // record how many elements are in the current level (traverse the current level, then add the next level)
		l := len(queue)
		for i := 0; i < l; i++ {
            // dequeue
			level := queue[0]
			queue = queue[1:]
			list = append(list, level.Val)
			if level.Left != nil {
				queue = append(queue, level.Left)
			}
			if level.Right != nil {
				queue = append(queue, level.Right)
			}
		}
		result = append(result, list)
	}
	return result
}
```

#### binary-tree-level-order-traversal-ii

[binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

> Given a binary tree, return its bottom-up level-order traversal of node values. (i.e. traverse from the level of the leaf nodes up to the level of the root, level by level from left to right)

Idea: based on level-order traversal, just reverse the result

```go
func levelOrderBottom(root *TreeNode) [][]int {
    result := levelOrder(root)
    // reverse the result
    reverse(result)
    return result
}
func reverse(nums [][]int) {
	for i, j := 0, len(nums)-1; i < j; i, j = i+1, j-1 {
		nums[i], nums[j] = nums[j], nums[i]
	}
}
func levelOrder(root *TreeNode) [][]int {
	result := make([][]int, 0)
	if root == nil {
		return result
	}
	queue := make([]*TreeNode, 0)
	queue = append(queue, root)
	for len(queue) > 0 {
		list := make([]int, 0)
        // why take the length?
        // record how many elements are in the current level (traverse the current level, then add the next level)
		l := len(queue)
		for i := 0; i < l; i++ {
            // dequeue
			level := queue[0]
			queue = queue[1:]
			list = append(list, level.Val)
			if level.Left != nil {
				queue = append(queue, level.Left)
			}
			if level.Right != nil {
				queue = append(queue, level.Right)
			}
		}
		result = append(result, list)
	}
	return result
}
```

#### binary-tree-zigzag-level-order-traversal

[binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

> Given a binary tree, return the zigzag level-order traversal of its node values. Z-shaped traversal

```go
func zigzagLevelOrder(root *TreeNode) [][]int {
	result := make([][]int, 0)
	if root == nil {
		return result
	}
	queue := make([]*TreeNode, 0)
	queue = append(queue, root)
	toggle := false
	for len(queue) > 0 {
		list := make([]int, 0)
		// record how many elements are in the current level (traverse the current level, then add the next level)
		l := len(queue)
		for i := 0; i < l; i++ {
			// dequeue
			level := queue[0]
			queue = queue[1:]
			list = append(list, level.Val)
			if level.Left != nil {
				queue = append(queue, level.Left)
			}
			if level.Right != nil {
				queue = append(queue, level.Right)
			}
		}
		if toggle {
			reverse(list)
		}
		result = append(result, list)
		toggle = !toggle
	}
	return result
}
func reverse(nums []int) {
	for i := 0; i < len(nums)/2; i++ {
		nums[i], nums[len(nums)-1-i] = nums[len(nums)-1-i], nums[i]
	}
}
```

### Binary Search Tree Applications

#### validate-binary-search-tree

[validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> Given a binary tree, determine whether it is a valid binary search tree.

Idea 1: inorder traversal, check whether the resulting list is already sorted

Idea 2: divide and conquer, check that left MAX < root < right MIN

```go
// v1
func isValidBST(root *TreeNode) bool {
    result := make([]int, 0)
    inOrder(root, &result)
    // check order
    for i := 0; i < len(result) - 1; i++{
        if result[i] >= result[i+1] {
            return false
        }
    }
    return true
}

func inOrder(root *TreeNode, result *[]int)  {
    if root == nil{
        return
    }
    inOrder(root.Left, result)
    *result = append(*result, root.Val)
    inOrder(root.Right, result)
}


```

```go
// v2 divide and conquer
type ResultType struct {
	IsValid bool
    // record the max and min values of the left and right sides, to compare with the root node
	Max     *TreeNode
	Min     *TreeNode
}

func isValidBST2(root *TreeNode) bool {
	result := helper(root)
	return result.IsValid
}
func helper(root *TreeNode) ResultType {
	result := ResultType{}
	// check
	if root == nil {
		result.IsValid = true
		return result
	}

	left := helper(root.Left)
	right := helper(root.Right)

	if !left.IsValid || !right.IsValid {
		result.IsValid = false
		return result
	}
	if left.Max != nil && left.Max.Val >= root.Val {
		result.IsValid = false
		return result
	}
	if right.Min != nil && right.Min.Val <= root.Val {
		result.IsValid = false
		return result
	}

	result.IsValid = true
    // if there is an even smaller 3 on the left, use the smaller node instead of 4
    //  5
    // / \
    // 1   4
    //      / \
    //     3   6
	result.Min = root
	if left.Min != nil {
		result.Min = left.Min
	}
	result.Max = root
	if right.Max != nil {
		result.Max = right.Max
	}
	return result
}
```

#### insert-into-a-binary-search-tree

[insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> Given the root node of a binary search tree (BST) and a value to insert into the tree, insert the value into the BST. Return the root node of the BST after insertion.

Idea: find the last leaf node that satisfies the insertion condition

```go
// DFS to find the insertion position
func insertIntoBST(root *TreeNode, val int) *TreeNode {
    if root == nil {
        root = &TreeNode{Val: val}
        return root
    }
    if root.Val > val {
        root.Left = insertIntoBST(root.Left, val)
    } else {
        root.Right = insertIntoBST(root.Right, val)
    }
    return root
}
```

## Summary

- Master recursive and iterative binary tree traversal
- Understand DFS preorder traversal and divide and conquer
- Understand BFS level-order traversal

## Exercises

- [ ] [maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)
- [ ] [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)
- [ ] [binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
- [ ] [lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [ ] [binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
- [ ] [binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)
- [ ] [binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
- [ ] [validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)
- [ ] [insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
