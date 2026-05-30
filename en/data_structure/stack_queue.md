# Stack and Queue

## Introduction

The characteristic of a stack is last-in, first-out (LIFO).

![image.png](https://cdn.jsdelivr.net/gh/greyireland/algorithm-pattern@master/images/stack.png)

Based on this property, we can temporarily save some data and later pop it back out in order when needed. It is commonly used in DFS (depth-first search).

A queue is generally used in BFS (breadth-first search), searching layer by layer.

## Stack

[min-stack](https://leetcode-cn.com/problems/min-stack/)

> Design a stack that supports push, pop, and top operations, and can retrieve the minimum element in constant time.

Idea: Implement it with two stacks; one min stack always keeps the minimum value on top.

```go
type MinStack struct {
    min []int
    stack []int
}


/** initialize your data structure here. */
func Constructor() MinStack {
    return MinStack{
        min: make([]int, 0),
        stack: make([]int, 0),
    }
}


func (this *MinStack) Push(x int)  {
    min := this.GetMin()
    if x < min {
        this.min = append(this.min, x)
    } else {
        this.min = append(this.min, min)
    }
    this.stack = append(this.stack, x)
}


func (this *MinStack) Pop()  {
    if len(this.stack) == 0 {
        return
    }
    this.stack = this.stack[:len(this.stack)-1]
    this.min = this.min[:len(this.min)-1]
}


func (this *MinStack) Top() int {
    if len(this.stack) == 0 {
        return 0
    }
    return this.stack[len(this.stack)-1]
}


func (this *MinStack) GetMin() int {
    if len(this.min) == 0 {
        return 1 << 31
    }
    min := this.min[len(this.min)-1]
    return min
}


/**
 * Your MinStack object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(x);
 * obj.Pop();
 * param_3 := obj.Top();
 * param_4 := obj.GetMin();
 */
```

[evaluate-reverse-polish-notation](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

> **Reverse Polish notation evaluation** > **Input:** `["2", "1", "+", "3", "*"]` > **Output:** 9
>
> **Explanation:** `((2 + 1) * 3) = 9`

Idea: Use a stack to save the original elements; when an operator is encountered, pop and compute, then push the result back, repeating this process.

```go
func evalRPN(tokens []string) int {
    if len(tokens)==0{
        return 0
    }
    stack:=make([]int,0)
    for i:=0;i<len(tokens);i++{
        switch tokens[i]{
        case "+","-","*","/":
            if len(stack)<2{
                return -1
            }
            // Note: a is the dividend, b is the divisor
            b:=stack[len(stack)-1]
            a:=stack[len(stack)-2]
            stack=stack[:len(stack)-2]
            var result int
            switch tokens[i]{
            case "+":
                result=a+b
            case "-":
                result=a-b
            case "*":
                result=a*b
            case "/":
                result=a/b
            }
            stack=append(stack,result)
        default:
            // Convert to a number
            val,_:=strconv.Atoi(tokens[i])
            stack=append(stack,val)
        }
    }
    return stack[0]
}
```

[decode-string](https://leetcode-cn.com/problems/decode-string/)

> Given an encoded string, return its decoded string.
> s = "3[a]2[bc]", returns "aaabcbc".
> s = "3[a2[c]]", returns "accaccacc".
> s = "2[abc]3[cd]ef", returns "abcabccdcdcdef".

Idea: Use a stack to assist with the operations.

```go
func decodeString(s string) string {
	if len(s) == 0 {
		return ""
	}
	stack := make([]byte, 0)
	for i := 0; i < len(s); i++ {
		switch s[i] {
		case ']':
			temp := make([]byte, 0)
			for len(stack) != 0 && stack[len(stack)-1] != '[' {
				v := stack[len(stack)-1]
				stack = stack[:len(stack)-1]
				temp = append(temp, v)
			}
			// pop '['
			stack = stack[:len(stack)-1]
			// pop num
			idx := 1
			for len(stack) >= idx && stack[len(stack)-idx] >= '0' && stack[len(stack)-idx] <= '9' {
				idx++
			}
            // Pay attention to index boundaries
			num := stack[len(stack)-idx+1:]
			stack = stack[:len(stack)-idx+1]
			count, _ := strconv.Atoi(string(num))
			for j := 0; j < count; j++ {
                // Put the characters back onto the stack in forward order
				for j := len(temp) - 1; j >= 0; j-- {
					stack = append(stack, temp[j])
				}
			}
		default:
			stack = append(stack, s[i])

		}
	}
	return string(stack)
}
```

Template for DFS recursive search using a stack

```go
boolean DFS(int root, int target) {
    Set<Node> visited;
    Stack<Node> s;
    add root to s;
    while (s is not empty) {
        Node cur = the top element in s;
        return true if cur is target;
        for (Node next : the neighbors of cur) {
            if (next is not in visited) {
                add next to s;
                add next to visited;
            }
        }
        remove cur from s;
    }
    return false;
}
```

[binary-tree-inorder-traversal](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

> Given a binary tree, return its *inorder* traversal.

```go
// Idea: use a stack to save the already-visited elements, so we can backtrack along the original path
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

[clone-graph](https://leetcode-cn.com/problems/clone-graph/)

> Given a reference to a node in an undirected connected graph, return a deep copy (clone) of the graph.

```go
func cloneGraph(node *Node) *Node {
    visited:=make(map[*Node]*Node)
    return clone(node,visited)
}
// 1 2
// 4 3
// Recursively clone, passing in the already-visited elements as a filter condition
func clone(node *Node,visited map[*Node]*Node)*Node{
    if node==nil{
        return nil
    }
    // If already visited, return directly
    if v,ok:=visited[node];ok{
        return v
    }
    newNode:=&Node{
        Val:node.Val,
        Neighbors:make([]*Node,len(node.Neighbors)),
    }
    visited[node]=newNode
    for i:=0;i<len(node.Neighbors);i++{
        newNode.Neighbors[i]=clone(node.Neighbors[i],visited)
    }
    return newNode
}
```

[number-of-islands](https://leetcode-cn.com/problems/number-of-islands/)

> Given a 2D grid composed of '1' (land) and '0' (water), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume that all four edges of the grid are surrounded by water.

Idea: Traverse the possibilities through depth-first search (note: mark visited elements).

```go

func numIslands(grid [][]byte) int {
    var count int
    for i:=0;i<len(grid);i++{
        for j:=0;j<len(grid[i]);j++{
            if grid[i][j]=='1' && dfs(grid,i,j)>=1{
                count++
            }
        }
    }
    return count
}
func dfs(grid [][]byte,i,j int)int{
    if i<0||i>=len(grid)||j<0||j>=len(grid[0]){
        return 0
    }
    if grid[i][j]=='1'{
        // Mark as visited (each cell only needs to be visited once)
        grid[i][j]=0
        return dfs(grid,i-1,j)+dfs(grid,i,j-1)+dfs(grid,i+1,j)+dfs(grid,i,j+1)+1
    }
    return 0
}
```

[largest-rectangle-in-histogram](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

> Given _n_ non-negative integers representing the heights of bars in a histogram, where each bar is adjacent to the others and has a width of 1.
> Find the largest rectangle that can be outlined in the histogram.

Idea: Compute the area with the current bar as the height; that is, it reduces to finding the nearest values on the left and right that are smaller than the current value.

![image.png](https://cdn.jsdelivr.net/gh/greyireland/algorithm-pattern@master/images/stack_rain.png)

Use a stack to save the left-side elements that are smaller than the current value.

![image.png](https://cdn.jsdelivr.net/gh/greyireland/algorithm-pattern@master/images/stack_rain2.png)

```go
func largestRectangleArea(heights []int) int {
	if len(heights) == 0 {
		return 0
	}
	stack := make([]int, 0)
	max := 0
	for i := 0; i <= len(heights); i++ {
		var cur int
		if i == len(heights) {
			cur = 0
		} else {
			cur = heights[i]
		}
        // If the current height is smaller than the top of the stack, pop all stack elements and compute the area
		for len(stack) != 0 && cur <= heights[stack[len(stack)-1]] {
			pop := stack[len(stack)-1]
			stack = stack[:len(stack)-1]
			h := heights[pop]
            // Compute the width
			w := i
			if len(stack) != 0 {
				peek := stack[len(stack)-1]
				w = i - peek - 1
			}
			max = Max(max, h*w)
		}
        // Record the index so we can obtain the corresponding element
		stack = append(stack, i)
	}
	return max
}
func Max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
```

## Queue

Commonly used in BFS (breadth-first search).

[implement-queue-using-stacks](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

> Implement a queue using stacks.

```go
type MyQueue struct {
    stack []int
    back  []int
}

/** Initialize your data structure here. */
func Constructor() MyQueue {
    return MyQueue{
        stack: make([]int, 0),
        back:  make([]int, 0),
    }
}

// 1
// 3
// 5

/** Push element x to the back of queue. */
func (this *MyQueue) Push(x int) {
    for len(this.back) != 0 {
        val := this.back[len(this.back)-1]
        this.back = this.back[:len(this.back)-1]
        this.stack = append(this.stack, val)
    }
    this.stack = append(this.stack, x)
}

/** Removes the element from in front of queue and returns that element. */
func (this *MyQueue) Pop() int {
    for len(this.stack) != 0 {
        val := this.stack[len(this.stack)-1]
        this.stack = this.stack[:len(this.stack)-1]
        this.back = append(this.back, val)
    }
    if len(this.back) == 0 {
        return 0
    }
    val := this.back[len(this.back)-1]
    this.back = this.back[:len(this.back)-1]
    return val
}

/** Get the front element. */
func (this *MyQueue) Peek() int {
    for len(this.stack) != 0 {
        val := this.stack[len(this.stack)-1]
        this.stack = this.stack[:len(this.stack)-1]
        this.back = append(this.back, val)
    }
    if len(this.back) == 0 {
        return 0
    }
    val := this.back[len(this.back)-1]
    return val
}

/** Returns whether the queue is empty. */
func (this *MyQueue) Empty() bool {
    return len(this.stack) == 0 && len(this.back) == 0
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(x);
 * param_2 := obj.Pop();
 * param_3 := obj.Peek();
 * param_4 := obj.Empty();
 */
```

Binary tree level-order traversal

```go
func levelOrder(root *TreeNode) [][]int {
    // Use the length of the previous level to determine the elements of the next level
    result := make([][]int, 0)
    if root == nil {
        return result
    }
    queue := make([]*TreeNode, 0)
    queue = append(queue, root)
    for len(queue) > 0 {
        list := make([]int, 0)
        // Why take the length?
        // To record how many elements are in the current level (traverse the current level, then add the next level)
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

[01-matrix](https://leetcode-cn.com/problems/01-matrix/)

> Given a matrix composed of 0s and 1s, find the distance of each element to the nearest 0.
> The distance between two adjacent elements is 1.

```go
// BFS: enqueue from the 0s, after dequeuing compute the results for up/down/left/right, then re-enqueue them for the next layer of operations
// 0 0 0 0
// 0 x 0 0
// x x x 0
// 0 x 0 0

// 0 0 0 0
// 0 1 0 0
// 1 x 1 0
// 0 1 0 0

// 0 0 0 0
// 0 1 0 0
// 1 2 1 0
// 0 1 0 0
func updateMatrix(matrix [][]int) [][]int {
    q:=make([][]int,0)
    for i:=0;i<len(matrix);i++{
        for j:=0;j<len(matrix[0]);j++{
            if matrix[i][j]==0{
                // enqueue
                point:=[]int{i,j}
                q=append(q,point)
            }else{
                matrix[i][j]=-1
            }
        }
    }
    directions:=[][]int{{0,1},{0,-1},{-1,0},{1,0}}
    for len(q)!=0{
        // dequeue
        point:=q[0]
        q=q[1:]
        for _,v:=range directions{
            x:=point[0]+v[0]
            y:=point[1]+v[1]
            if x>=0&&x<len(matrix)&&y>=0&&y<len(matrix[0])&&matrix[x][y]==-1{
                matrix[x][y]=matrix[point[0]][point[1]]+1
                // Enqueue the current element to perform one more round of BFS
                q=append(q,[]int{x,y})
            }
        }
    }
    return matrix

}
```

## Summary

- Get familiar with the use cases of stacks
  - Last-in, first-out; saving temporary values
  - Using a stack for DFS (depth-first search)
- Get familiar with the use cases of queues
  - Using a queue for BFS (breadth-first search)

## Exercises

- [ ] [min-stack](https://leetcode-cn.com/problems/min-stack/)
- [ ] [evaluate-reverse-polish-notation](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)
- [ ] [decode-string](https://leetcode-cn.com/problems/decode-string/)
- [ ] [binary-tree-inorder-traversal](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)
- [ ] [clone-graph](https://leetcode-cn.com/problems/clone-graph/)
- [ ] [number-of-islands](https://leetcode-cn.com/problems/number-of-islands/)
- [ ] [largest-rectangle-in-histogram](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)
- [ ] [implement-queue-using-stacks](https://leetcode-cn.com/problems/implement-queue-using-stacks/)
- [ ] [01-matrix](https://leetcode-cn.com/problems/01-matrix/)
