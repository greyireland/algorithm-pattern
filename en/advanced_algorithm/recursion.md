# Recursion

## Introduction

Transform a big problem into smaller problems, and solve each small problem one by one through recursion.

## Examples

[reverse-string](https://leetcode-cn.com/problems/reverse-string/)

> Write a function that reverses the input string. The input string is given as a character array `char[]`.

```go
func reverseString(s []byte) {
	res := make([]byte, 0)
	reverse(s, 0, &res)
	for i := 0; i < len(s); i++ {
		s[i] = res[i]
	}
}
func reverse(s []byte, i int, res *[]byte) {
	if i == len(s) {
		return
	}
	reverse(s, i+1, res)
	*res = append(*res, s[i])
}
```

[swap-nodes-in-pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

> Given a linked list, swap every two adjacent nodes and return the linked list after swapping.
> **You may not simply modify the values inside the nodes**, but instead need to actually swap the nodes.

```go
func swapPairs(head *ListNode) *ListNode {
    // Idea: turn reversing the linked list into a subproblem, then solve it one step at a time through recursion
    // First reverse two nodes, then continue reversing the following nodes the same way, then connect these reversed nodes together
    return helper(head)
}
func helper(head *ListNode)*ListNode{
    if head==nil||head.Next==nil{
        return head
    }
    // Save the head pointer of the next stage
    nextHead:=head.Next.Next
    // Reverse the pointers of the current stage
    next:=head.Next
    next.Next=head
    head.Next=helper(nextHead)
    return next
}
```

[unique-binary-search-trees-ii](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

> Given an integer n, generate all binary search trees whose nodes are made up of 1 ... n.

```go
func generateTrees(n int) []*TreeNode {
    if n==0{
        return nil
    }
    return generate(1,n)

}
func generate(start,end int)[]*TreeNode{
    if start>end{
        return []*TreeNode{nil}
    }
    ans:=make([]*TreeNode,0)
    for i:=start;i<=end;i++{
        // Recursively generate all left and right subtrees
        lefts:=generate(start,i-1)
        rights:=generate(i+1,end)
        // Combine the left and right subtrees, then return
        for j:=0;j<len(lefts);j++{
            for k:=0;k<len(rights);k++{
                root:=&TreeNode{Val:i}
                root.Left=lefts[j]
                root.Right=rights[k]
                ans=append(ans,root)
            }
        }
    }
    return ans
}
```

## Recursion + Memoization

[fibonacci-number](https://leetcode-cn.com/problems/fibonacci-number/)

> The Fibonacci numbers, usually denoted F(n), form a sequence called the Fibonacci sequence. The sequence starts with 0 and 1, and each subsequent number is the sum of the previous two. That is:
> F(0) = 0,   F(1) = 1
> F(N) = F(N - 1) + F(N - 2), where N > 1.
> Given N, compute F(N).

```go
func fib(N int) int {
    return dfs(N)
}
var m map[int]int=make(map[int]int)
func dfs(n int)int{
    if n < 2{
        return n
    }
    // Read from cache
    if m[n]!=0{
        return m[n]
    }
    ans:=dfs(n-2)+dfs(n-1)
    // Cache the already computed value
    m[n]=ans
    return ans
}
```

## Exercises

- [ ] [reverse-string](https://leetcode-cn.com/problems/reverse-string/)
- [ ] [swap-nodes-in-pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)
- [ ] [unique-binary-search-trees-ii](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)
- [ ] [fibonacci-number](https://leetcode-cn.com/problems/fibonacci-number/)
