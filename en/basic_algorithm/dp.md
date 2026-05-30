# Dynamic Programming

## Background

Let's start with a problem~

For example: [triangle](https://leetcode-cn.com/problems/triangle/)

> Given a triangle, find the minimum path sum from top to bottom. Each step you may move to an adjacent node in the row below.

For example, given the following triangle:

```text
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```

The minimum path sum from top to bottom is 11 (i.e., 2 + 3 + 5 + 1 = 11).

Use DFS (traversal or divide and conquer)

Traversal

![image.png](https://cdn.jsdelivr.net/gh/greyireland/algorithm-pattern@master/images/dp_triangle.png)

Divide and conquer

![image.png](https://cdn.jsdelivr.net/gh/greyireland/algorithm-pattern@master/images/dp_dc.png)

Optimize DFS by caching values that have already been computed (called: memoization; essentially: dynamic programming)

![image.png](https://cdn.jsdelivr.net/gh/greyireland/algorithm-pattern@master/images/dp_memory_search.png)

Dynamic programming turns a big problem into small problems, and the method that solves the issue of repeated computation of subproblems is called dynamic programming.

The difference between dynamic programming and DFS

- For a binary tree, the subproblems have no intersection, so most binary tree problems can be solved with recursion or divide and conquer, i.e., DFS.
- For a problem like triangle, where there are repeated paths, **the subproblems intersect**, so it can be solved with dynamic programming.

Dynamic programming, bottom-up

```go
func minimumTotal(triangle [][]int) int {
	if len(triangle) == 0 || len(triangle[0]) == 0 {
		return 0
	}
	// 1. State definition: f[i][j] represents the shortest path from i,j to the last row
	var l = len(triangle)
	var f = make([][]int, l)
	// 2. Initialization
	for i := 0; i < l; i++ {
		for j := 0; j < len(triangle[i]); j++ {
			if f[i] == nil {
				f[i] = make([]int, len(triangle[i]))
			}
			f[i][j] = triangle[i][j]
		}
	}
	// 3. Solve by recurrence
	for i := len(triangle) - 2; i >= 0; i-- {
		for j := 0; j < len(triangle[i]); j++ {
			f[i][j] = min(f[i+1][j], f[i+1][j+1]) + triangle[i][j]
		}
	}
	// 4. Answer
	return f[0][0]
}
func min(a, b int) int {
	if a > b {
		return b
	}
	return a
}

```

Dynamic programming, top-down

```go
// Test case:
// [
// [2],
// [3,4],
// [6,5,7],
// [4,1,8,3]
// ]
func minimumTotal(triangle [][]int) int {
    if len(triangle) == 0 || len(triangle[0]) == 0 {
        return 0
    }
    // 1. State definition: f[i][j] represents the shortest path from 0,0 to i,j
    var l = len(triangle)
    var f = make([][]int, l)
    // 2. Initialization
    for i := 0; i < l; i++ {
        for j := 0; j < len(triangle[i]); j++ {
            if f[i] == nil {
                f[i] = make([]int, len(triangle[i]))
            }
            f[i][j] = triangle[i][j]
        }
    }
    // Solve by recurrence
    for i := 1; i < l; i++ {
        for j := 0; j < len(triangle[i]); j++ {
            // There are two cases here:
            // 1. The previous row has no left value
            // 2. The previous row has no right value
            if j-1 < 0 {
                f[i][j] = f[i-1][j] + triangle[i][j]
            } else if j >= len(f[i-1]) {
                f[i][j] = f[i-1][j-1] + triangle[i][j]
            } else {
                f[i][j] = min(f[i-1][j], f[i-1][j-1]) + triangle[i][j]
            }
        }
    }
    result := f[l-1][0]
    for i := 1; i < len(f[l-1]); i++ {
        result = min(result, f[l-1][i])
    }
    return result
}
func min(a, b int) int {
    if a > b {
        return b
    }
    return a
}
```

## Relationship Between Recursion and Dynamic Programming

Recursion is a way of implementing a program: a function calling itself

```go
Function(x) {
	...
	Funciton(x-1);
	...
}
```

Dynamic programming: it is a way of thinking to solve problems, where the result of a large-scale problem is computed from the results of smaller-scale problems. Dynamic programming can be implemented with recursion (Memorization Search).

## Use Cases

Two conditions must be satisfied:

- Satisfy one of the following:
  - Find the maximum/minimum value (Maximum/Minimum)
  - Determine feasibility (Yes/No)
  - Count the number of feasible solutions (Count(\*))
- Cannot be sorted or swapped (Can not sort / swap)

For example: [longest-consecutive-sequence](https://leetcode-cn.com/problems/longest-consecutive-sequence/) — positions can be swapped, so dynamic programming is not used.

## Four Key Elements

1. **State**
   - Inspiration, creativity; storing the results of small-scale problems
2. Function
   - The relationship between states; how to compute a larger state from smaller states
3. Initialization
   - What is the smallest extreme state, the starting point
4. Answer
   - What is the largest state, the ending point

## Four Common Types

1. Matrix DP (10%)
1. Sequence (40%)
1. Two Sequences DP (40%)
1. Backpack (10%)

> Notes
>
> - Most greedy algorithm problems rely on memorizing answers, so if dynamic programming can be used, prefer dynamic programming over greedy algorithms.

## 1. Matrix Type (10%)

### [minimum-path-sum](https://leetcode-cn.com/problems/minimum-path-sum/)

> Given an *m* x *n* grid filled with non-negative integers, find a path from the top-left corner to the bottom-right corner that minimizes the sum of the numbers along the path.

Idea: dynamic programming
1. state: f[x][y] is the shortest path from the start to x,y
2. function: f[x][y] = min(f[x-1][y], f[x][y-1]) + A[x][y]
3. initialize: f[0][0] = A[0][0], f[i][0] = sum(0,0 -> i,0), f[0][i] = sum(0,0 -> 0,i)
4. answer: f[n-1][m-1]

```go
func minPathSum(grid [][]int) int {
    // Idea: dynamic programming
    // f[i][j] represents the minimum sum from i,j to 0,0
    if len(grid) == 0 || len(grid[0]) == 0 {
        return 0
    }
    // Reuse the original matrix list
    // Initialization: f[i][0], f[0][j]
    for i := 1; i < len(grid); i++ {
        grid[i][0] = grid[i][0] + grid[i-1][0]
    }
    for j := 1; j < len(grid[0]); j++ {
        grid[0][j] = grid[0][j] + grid[0][j-1]
    }
    for i := 1; i < len(grid); i++ {
        for j := 1; j < len(grid[i]); j++ {
            grid[i][j] = min(grid[i][j-1], grid[i-1][j]) + grid[i][j]
        }
    }
    return grid[len(grid)-1][len(grid[0])-1]
}
func min(a, b int) int {
    if a > b {
        return b
    }
    return a
}
```

### [unique-paths](https://leetcode-cn.com/problems/unique-paths/)

> A robot is located at the top-left corner of an m x n grid (the start point is marked "Start" in the figure below).
> The robot can only move down or right one step at a time. The robot is trying to reach the bottom-right corner of the grid (marked "Finish" in the figure below).
> How many distinct paths are there in total?

```go
func uniquePaths(m int, n int) int {
	// f[i][j] represents the number of paths from i,j to 0,0
	f := make([][]int, m)
	for i := 0; i < m; i++ {
		for j := 0; j < n; j++ {
			if f[i] == nil {
				f[i] = make([]int, n)
			}
			f[i][j] = 1
		}
	}
	for i := 1; i < m; i++ {
		for j := 1; j < n; j++ {
			f[i][j] = f[i-1][j] + f[i][j-1]
		}
	}
	return f[m-1][n-1]
}
```

### [unique-paths-ii](https://leetcode-cn.com/problems/unique-paths-ii/)

> A robot is located at the top-left corner of an m x n grid (the start point is marked "Start" in the figure below).
> The robot can only move down or right one step at a time. The robot is trying to reach the bottom-right corner of the grid (marked "Finish" in the figure below).
> How many distinct paths are there in total?
> Now consider that there are obstacles in the grid. How many distinct paths are there from the top-left corner to the bottom-right corner?

```go
func uniquePathsWithObstacles(obstacleGrid [][]int) int {
	// f[i][j] = f[i-1][j] + f[i][j-1] and check for obstacles
	if obstacleGrid[0][0] == 1 {
		return 0
	}
	m := len(obstacleGrid)
	n := len(obstacleGrid[0])
	f := make([][]int, m)
	for i := 0; i < m; i++ {
		for j := 0; j < n; j++ {
			if f[i] == nil {
				f[i] = make([]int, n)
			}
			f[i][j] = 1
		}
	}
	for i := 1; i < m; i++ {
		if obstacleGrid[i][0] == 1 || f[i-1][0] == 0 {
			f[i][0] = 0
		}
	}
	for j := 1; j < n; j++ {
		if obstacleGrid[0][j] == 1 || f[0][j-1] == 0 {
			f[0][j] = 0
		}
	}
	for i := 1; i < m; i++ {
		for j := 1; j < n; j++ {
			if obstacleGrid[i][j] == 1 {
				f[i][j] = 0
			} else {
				f[i][j] = f[i-1][j] + f[i][j-1]
			}
		}
	}
	return f[m-1][n-1]
}
```

## 2. Sequence Type (40%)

### [climbing-stairs](https://leetcode-cn.com/problems/climbing-stairs/)

> Suppose you are climbing stairs. It takes *n* steps to reach the top.

```go
func climbStairs(n int) int {
    // f[i] = f[i-1] + f[i-2]
    if n == 1 || n == 0 {
        return n
    }
    f := make([]int, n+1)
    f[1] = 1
    f[2] = 2
    for i := 3; i <= n; i++ {
        f[i] = f[i-1] + f[i-2]
    }
    return f[n]
}
```

### [jump-game](https://leetcode-cn.com/problems/jump-game/)

> Given an array of non-negative integers, you are initially positioned at the first index of the array.
> Each element in the array represents your maximum jump length at that position.
> Determine if you are able to reach the last index.

```go
func canJump(nums []int) bool {
    // Idea: look at the last jump
    // State: f[i] represents whether you can jump from 0 to i
    // Derivation: f[i] = OR(f[j], j<i && j can jump to i) — check whether the last jump from any previous point can reach the current point
    // Initialization: f[0] = 0
    // Result: f[n-1]
    if len(nums) == 0 {
        return true
    }
    f := make([]bool, len(nums))
    f[0] = true
    for i := 1; i < len(nums); i++ {
        for j := 0; j < i; j++ {
            if f[j] == true && nums[j]+j >= i {
                f[i] = true
            }
        }
    }
    return f[len(nums)-1]
}
```

### [jump-game-ii](https://leetcode-cn.com/problems/jump-game-ii/)

> Given an array of non-negative integers, you are initially positioned at the first index of the array.
> Each element in the array represents your maximum jump length at that position.
> Your goal is to reach the last index in the minimum number of jumps.

```go
// v1 dynamic programming (other languages may time out; refer to v2)
func jump(nums []int) int {
    // State: f[i] represents the minimum number of jumps from the start to the current position
    // Derivation: f[i] = f[j], a[j]+j >= i, min(f[j]+1)
    // Initialization: f[0] = 0
    // Result: f[n-1]
    f := make([]int, len(nums))
    f[0] = 0
    for i := 1; i < len(nums); i++ {
        // The maximum value of f[i] is i
        f[i] = i
        // Iterate over previous results and take the minimum + 1
        for j := 0; j < i; j++ {
            if nums[j]+j >= i {
                f[i] = min(f[j]+1,f[i])
            }
        }
    }
    return f[len(nums)-1]
}
func min(a, b int) int {
    if a > b {
        return b
    }
    return a
}
```

```go
// v2 dynamic programming + greedy optimization
func jump(nums []int) int {
    n:=len(nums)
    f := make([]int, n)
    f[0] = 0
    for i := 1; i < n; i++ {
        // Take the first point that can jump to the current position
        // Since the result set of jump counts is monotonically increasing, the greedy approach is correct
        idx:=0
        for idx<n&&idx+nums[idx]<i{
            idx++
        }
        f[i]=f[idx]+1
    }
    return f[n-1]
}

```

### [palindrome-partitioning-ii](https://leetcode-cn.com/problems/palindrome-partitioning-ii/)

> Given a string _s_, partition _s_ such that every substring of the partition is a palindrome.
> Return the minimum number of cuts needed.

```go
func minCut(s string) int {
	// state: f[i] is the minimum number of cuts needed for the substring formed by the "first i" characters (count - 1 is the index)
	// function: f[i] = MIN{f[j]+1}, j < i && [j+1 ~ i] is a palindrome
	// initialize: f[i] = i - 1 (f[0] = -1)
	// answer: f[s.length()]
	if len(s) == 0 || len(s) == 1 {
		return 0
	}
	f := make([]int, len(s)+1)
	f[0] = -1
	f[1] = 0
	for i := 1; i <= len(s); i++ {
		f[i] = i - 1
		for j := 0; j < i; j++ {
			if isPalindrome(s, j, i-1) {
				f[i] = min(f[i], f[j]+1)
			}
		}
	}
	return f[len(s)]
}
func min(a, b int) int {
	if a > b {
		return b
	}
	return a
}
func isPalindrome(s string, i, j int) bool {
	for i < j {
		if s[i] != s[j] {
			return false
		}
		i++
		j--
	}
	return true
}
```

Note

- When checking palindromic substrings, you can precompute them with dynamic programming to reduce time complexity.

### [longest-increasing-subsequence](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

> Given an unsorted array of integers, find the length of the longest increasing subsequence.

```go
func lengthOfLIS(nums []int) int {
    // f[i] represents the length of the longest subsequence ending at i, starting from 0
    // f[i] = max(f[j])+1, a[j]<a[i]
    // f[0...n-1] = 1
    // max(f[0]...f[n-1])
    if len(nums) == 0 || len(nums) == 1 {
        return len(nums)
    }
    f := make([]int, len(nums))
    f[0] = 1
    for i := 1; i < len(nums); i++ {
        f[i] = 1
        for j := 0; j < i; j++ {
            if nums[j] < nums[i] {
                f[i] = max(f[i], f[j]+1)
            }
        }
    }
    result := f[0]
    for i := 1; i < len(nums); i++ {
        result = max(result, f[i])
    }
    return result

}
func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```

### [word-break](https://leetcode-cn.com/problems/word-break/)

> Given a **non-empty** string *s* and a dictionary *wordDict* containing a list of **non-empty** words, determine whether *s* can be segmented into a space-separated sequence of one or more dictionary words.

```go
func wordBreak(s string, wordDict []string) bool {
	// f[i] represents whether the first i characters can be segmented
	// f[i] = f[j] && s[j+1~i] in wordDict
	// f[0] = true
	// return f[len]

	if len(s) == 0 {
		return true
	}
	f := make([]bool, len(s)+1)
	f[0] = true
	max,dict := maxLen(wordDict)
	for i := 1; i <= len(s); i++ {
		l := 0
		if i - max > 0 {
			l = i - max
		}
		for j := l; j < i; j++ {
			if f[j] && inDict(s[j:i],dict) {
				f[i] = true
                break
			}
		}
	}
	return f[len(s)]
}



func maxLen(wordDict []string) (int,map[string]bool) {
    dict := make(map[string]bool)
	max := 0
	for _, v := range wordDict {
		dict[v] = true
		if len(v) > max {
			max = len(v)
		}
	}
	return max,dict
}

func inDict(s string,dict map[string]bool) bool {
	_, ok := dict[s]
	return ok
}

```

Summary

A common approach is to use position 0 as a placeholder, so that problems can be handled uniformly. The initialization then uses length+1 on top of the original, and the result returned is f[n].

- The state can be "the first i"
- Initialize with length+1
- Access with index = i-1
- Return value: f[n] or f[m][n]

## Two Sequences DP (40%)

### [longest-common-subsequence](https://leetcode-cn.com/problems/longest-common-subsequence/)

> Given two strings text1 and text2, return the longest common subsequence of these two strings.
> A subsequence of a string is a new string generated from the original string with some characters (can be none) deleted without changing the relative order of the remaining characters.
> For example, "ace" is a subsequence of "abcde", but "aec" is not a subsequence of "abcde". A "common subsequence" of two strings is a subsequence that is common to both strings.

```go
func longestCommonSubsequence(a string, b string) int {
    // dp[i][j] is the longest common subsequence of the first i characters of a and the first j characters of b
    // dp[m+1][n+1]
    //   ' a d c e
    // ' 0 0 0 0 0
    // a 0 1 1 1 1
    // c 0 1 1 2 1
    //
    dp:=make([][]int,len(a)+1)
    for i:=0;i<=len(a);i++ {
        dp[i]=make([]int,len(b)+1)
    }
    for i:=1;i<=len(a);i++ {
        for j:=1;j<=len(b);j++ {
            // If equal, take the top-left element + 1; otherwise take the larger of left or top
            if a[i-1]==b[j-1] {
                dp[i][j]=dp[i-1][j-1]+1
            } else {
                dp[i][j]=max(dp[i-1][j],dp[i][j-1])
            }
        }
    }
    return dp[len(a)][len(b)]
}
func max(a,b int)int {
    if a>b{
        return a
    }
    return b
}
```

Note

- Go slice initialization

```go
dp:=make([][]int,len(a)+1)
for i:=0;i<=len(a);i++ {
    dp[i]=make([]int,len(b)+1)
}
```

- Iterate from 1 to the maximum length
- The index needs to be decremented by one

### [edit-distance](https://leetcode-cn.com/problems/edit-distance/)

> Given two words word1 and word2, compute the minimum number of operations required to convert word1 into word2.
> You may perform the following three operations on a word:
> Insert a character
> Delete a character
> Replace a character

Idea: very similar to the previous problem. If equal, no operation is needed; otherwise take the minimum of the delete, insert, and replace operations + 1.

```go
func minDistance(word1 string, word2 string) int {
    // dp[i][j] represents the minimum number of operations to edit the first i characters of string a into the first j characters of string b
    // dp[i][j] = OR(dp[i-1][j-1], a[i]==b[j], min(dp[i-1][j],dp[i][j-1],dp[i-1][j-1])+1)
    dp:=make([][]int,len(word1)+1)
    for i:=0;i<len(dp);i++{
        dp[i]=make([]int,len(word2)+1)
    }
    for i:=0;i<len(dp);i++{
        dp[i][0]=i
    }
    for j:=0;j<len(dp[0]);j++{
        dp[0][j]=j
    }
    for i:=1;i<=len(word1);i++{
        for j:=1;j<=len(word2);j++{
            // If equal, no operation is needed
            if word1[i-1]==word2[j-1] {
                dp[i][j]=dp[i-1][j-1]
            }else{ // Otherwise take the minimum of delete, insert, replace + 1
                dp[i][j]=min(min(dp[i-1][j],dp[i][j-1]),dp[i-1][j-1])+1
            }
        }
    }
    return dp[len(word1)][len(word2)]
}
func min(a,b int)int{
    if a>b{
        return b
    }
    return a
}
```

Note

> Another approach: MAXLEN(a,b)-LCS(a,b)

## Coin Change and Backpack (10%)

### [coin-change](https://leetcode-cn.com/problems/coin-change/)

> Given coins of different denominations and a total amount, write a function to compute the fewest number of coins needed to make up that amount. If that amount cannot be made up by any combination of the coins, return -1.

Idea: a bit different from other DP problems — here i represents money or capacity.

```go
func coinChange(coins []int, amount int) int {
    // State dp[i] represents the minimum number of coins to make up amount i
    // Derivation dp[i] = min(dp[i-1], dp[i-2], dp[i-5])+1, on the condition that i-coins[j] > 0
    // Initialize to the maximum value dp[i]=amount+1
    // Return value dp[n], or if dp[n]>amount => -1
    dp:=make([]int,amount+1)
    for i:=0;i<=amount;i++{
        dp[i]=amount+1
    }
    dp[0]=0
    for i:=1;i<=amount;i++{
        for j:=0;j<len(coins);j++{
            if  i-coins[j]>=0  {
                dp[i]=min(dp[i],dp[i-coins[j]]+1)
            }
        }
    }
    if dp[amount] > amount {
        return -1
    }
    return dp[amount]

}
func min(a,b int)int{
    if a>b{
        return b
    }
    return a
}
```

Note

> dp[i-a[j]] decides whether a[j] participates

### [backpack](https://www.lintcode.com/problem/backpack/description)

> Given n items, select some items to put into a backpack. How full can it be at most? Assume the backpack size is m, and the size of each item is A[i].

```go
func backPack (m int, A []int) int {
    // write your code here
    // f[i][j] represents whether the first i items can fill capacity j
    // f[i][j] = f[i-1][j], f[i-1][j-a[i]] when j>a[i]
    // f[0][0]=true f[...][0]=true
    // f[n][X]
    f:=make([][]bool,len(A)+1)
    for i:=0;i<=len(A);i++{
        f[i]=make([]bool,m+1)
    }
    f[0][0]=true
    for i:=1;i<=len(A);i++{
        for j:=0;j<=m;j++{
            f[i][j]=f[i-1][j]
            if j-A[i-1]>=0 && f[i-1][j-A[i-1]]{
                f[i][j]=true
            }
        }
    }
    for i:=m;i>=0;i--{
        if f[len(A)][i] {
            return i
        }
    }
    return 0
}

```

### [backpack-ii](https://www.lintcode.com/problem/backpack-ii/description)

> There are `n` items and a backpack of size `m`. The array `A` gives the size of each item and the array `V` gives the value of each item.
> What is the maximum total value that can be put into the backpack?

Idea: f[i][j] is the maximum value of putting the first i items into a backpack of capacity j

```go
func backPackII (m int, A []int, V []int) int {
    // write your code here
    // f[i][j] is the maximum value of putting the first i items into a backpack of capacity j
    // f[i][j] = max(f[i-1][j], f[i-1][j-A[i]]+V[i]) — whether to add item A[i]
    // f[0][0]=0 f[0][...]=0 f[...][0]=0
    f:=make([][]int,len(A)+1)
    for i:=0;i<len(A)+1;i++{
        f[i]=make([]int,m+1)
    }
    for i:=1;i<=len(A);i++{
        for j:=0;j<=m;j++{
            f[i][j]=f[i-1][j]
            if j-A[i-1] >= 0{
                f[i][j]=max(f[i-1][j],f[i-1][j-A[i-1]]+V[i-1])
            }
        }
    }
    return f[len(A)][m]
}
func max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
```

## Exercises

Matrix DP (10%)

- [ ] [triangle](https://leetcode-cn.com/problems/triangle/)
- [ ] [minimum-path-sum](https://leetcode-cn.com/problems/minimum-path-sum/)
- [ ] [unique-paths](https://leetcode-cn.com/problems/unique-paths/)
- [ ] [unique-paths-ii](https://leetcode-cn.com/problems/unique-paths-ii/)

Sequence (40%)

- [ ] [climbing-stairs](https://leetcode-cn.com/problems/climbing-stairs/)
- [ ] [jump-game](https://leetcode-cn.com/problems/jump-game/)
- [ ] [jump-game-ii](https://leetcode-cn.com/problems/jump-game-ii/)
- [ ] [palindrome-partitioning-ii](https://leetcode-cn.com/problems/palindrome-partitioning-ii/)
- [ ] [longest-increasing-subsequence](https://leetcode-cn.com/problems/longest-increasing-subsequence/)
- [ ] [word-break](https://leetcode-cn.com/problems/word-break/)

Two Sequences DP (40%)

- [ ] [longest-common-subsequence](https://leetcode-cn.com/problems/longest-common-subsequence/)
- [ ] [edit-distance](https://leetcode-cn.com/problems/edit-distance/)

Backpack & Coin Change (10%)

- [ ] [coin-change](https://leetcode-cn.com/problems/coin-change/)
- [ ] [backpack](https://www.lintcode.com/problem/backpack/description)
- [ ] [backpack-ii](https://www.lintcode.com/problem/backpack-ii/description)
