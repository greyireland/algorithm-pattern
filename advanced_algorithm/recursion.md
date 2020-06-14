# 递归

## 介绍

将大问题转化为小问题，通过递归依次解决各个小问题

## 示例

[reverse-string](https://leetcode-cn.com/problems/reverse-string/)

> 编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组  `char[]`  的形式给出。

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

> 给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。
> **你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

```go
func swapPairs(head *ListNode) *ListNode {
    // 思路：将链表翻转转化为一个子问题，然后通过递归方式依次解决
    // 先翻转两个，然后将后面的节点继续这样翻转，然后将这些翻转后的节点连接起来
    return helper(head)
}
func helper(head *ListNode)*ListNode{
    if head==nil||head.Next==nil{
        return head
    }
    // 保存下一阶段的头指针
    nextHead:=head.Next.Next
    // 翻转当前阶段指针
    next:=head.Next
    next.Next=head
    head.Next=helper(nextHead)
    return next
}
```

[unique-binary-search-trees-ii](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

> 给定一个整数 n，生成所有由 1 ... n 为节点所组成的二叉搜索树。

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
        // 递归生成所有左右子树
        lefts:=generate(start,i-1)
        rights:=generate(i+1,end)
        // 拼接左右子树后返回
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

## 递归+备忘录

[fibonacci-number](https://leetcode-cn.com/problems/fibonacci-number/)

> 斐波那契数，通常用  F(n) 表示，形成的序列称为斐波那契数列。该数列由  0 和 1 开始，后面的每一项数字都是前面两项数字的和。也就是：
> F(0) = 0,   F(1) = 1
> F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
> 给定  N，计算  F(N)。

```go
func fib(N int) int {
    return dfs(N)
}
var m map[int]int=make(map[int]int)
func dfs(n int)int{
    if n < 2{
        return n
    }
    // 读取缓存
    if m[n]!=0{
        return m[n]
    }
    ans:=dfs(n-2)+dfs(n-1)
    // 缓存已经计算过的值
    m[n]=ans
    return ans
}
```

## 练习

- [ ] [reverse-string](https://leetcode-cn.com/problems/reverse-string/)
- [ ] [swap-nodes-in-pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)
- [ ] [unique-binary-search-trees-ii](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)
- [ ] [fibonacci-number](https://leetcode-cn.com/problems/fibonacci-number/)
