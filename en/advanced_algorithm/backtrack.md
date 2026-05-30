# Backtracking

## Background

Backtracking is commonly used to traverse all subsets of a list. It is a form of DFS (depth-first search), generally used for permutations and exhaustively enumerating all possibilities. The traversal process is actually the traversal of a decision tree. The time complexity is generally O(N!). Unlike dynamic programming, it has no overlapping subproblems that can be optimized; backtracking is pure brute-force enumeration, and its complexity is generally very high.

## Template

```go
result = []
func backtrack(choice list, path):
    if meets the termination condition:
        result.add(path)
        return
    for choice in choice list:
        make a choice
        backtrack(choice list, path)
        undo the choice
```

The core idea is to make a choice from the choice list, then keep recursing downward to search for the answer. If a path leads nowhere, return and undo this choice.

## Examples

### [subsets](https://leetcode-cn.com/problems/subsets/)

> Given an integer array nums containing no duplicate elements, return all possible subsets (the power set) of the array.

Traversal process

![image.png](https://cdn.jsdelivr.net/gh/greyireland/algorithm-pattern@master/images/backtrack.png)

```go
func subsets(nums []int) [][]int {
	// Save the final result
	result := make([][]int, 0)
	// Save the intermediate result
	list := make([]int, 0)
	backtrack(nums, 0, list, &result)
	return result
}

// nums the given set
// pos the index position of the next element to add to the set
// list temporary result set (needs to be copied and saved each time)
// result the final result
func backtrack(nums []int, pos int, list []int, result *[][]int) {
	// Copy the temporary result out and save it to the final result
	ans := make([]int, len(list))
	copy(ans, list)
	*result = append(*result, ans)
	// Make a choice, process the result, then undo the choice
	for i := pos; i < len(nums); i++ {
		list = append(list, nums[i])
		backtrack(nums, i+1, list, result)
		list = list[0 : len(list)-1]
	}
}
```

### [subsets-ii](https://leetcode-cn.com/problems/subsets-ii/)

> Given an integer array nums that may contain duplicate elements, return all possible subsets (the power set) of the array. Note: the solution set must not contain duplicate subsets.

```go
import (
	"sort"
)

func subsetsWithDup(nums []int) [][]int {
	// Save the final result
	result := make([][]int, 0)
	// Save the intermediate result
	list := make([]int, 0)
	// Sort first
	sort.Ints(nums)
	backtrack(nums, 0, list, &result)
	return result
}

// nums the given set
// pos the index position of the next element to add to the set
// list temporary result set (needs to be copied and saved each time)
// result the final result
func backtrack(nums []int, pos int, list []int, result *[][]int) {
	// Copy the temporary result out and save it to the final result
	ans := make([]int, len(list))
	copy(ans, list)
	*result = append(*result, ans)
	// When making a choice, prune, process, then undo the choice
	for i := pos; i < len(nums); i++ {
        // After sorting, if we encounter a duplicate element again, do not choose this element
		if i != pos && nums[i] == nums[i-1] {
			continue
		}
		list = append(list, nums[i])
		backtrack(nums, i+1, list, result)
		list = list[0 : len(list)-1]
	}
}
```

### [permutations](https://leetcode-cn.com/problems/permutations/)

> Given a sequence with no duplicate numbers, return all of its possible permutations.

Idea: we need to record which elements have already been chosen, and only return results that meet the condition.

```go
func permute(nums []int) [][]int {
    result := make([][]int, 0)
    list := make([]int, 0)
    // Mark whether this element has already been added to the result set
    visited := make([]bool, len(nums))
    backtrack(nums, visited, list, &result)
    return result
}

// nums the input set
// visited the elements marked during the current recursion
// list temporary result set (path)
// result the final result
func backtrack(nums []int, visited []bool, list []int, result *[][]int) {
    // Return condition: the temporary result is a full permutation only when its length matches the input set
    if len(list) == len(nums) {
        ans := make([]int, len(list))
        copy(ans, list)
        *result = append(*result, ans)
        return
    }
    for i := 0; i < len(nums); i++ {
        // Skip elements that have already been added
        if visited[i] {
            continue
        }
        // Add the element
        list = append(list, nums[i])
        visited[i] = true
        backtrack(nums, visited, list, result)
        // Remove the element
        visited[i] = false
        list = list[0 : len(list)-1]
    }
}
```

### [permutations-ii](https://leetcode-cn.com/problems/permutations-ii/)

> Given a sequence that may contain duplicate numbers, return all unique permutations.

```go
import (
	"sort"
)

func permuteUnique(nums []int) [][]int {
	result := make([][]int, 0)
	list := make([]int, 0)
	// Mark whether this element has already been added to the result set
	visited := make([]bool, len(nums))
	sort.Ints(nums)
	backtrack(nums, visited, list, &result)
	return result
}

// nums the input set
// visited the elements marked during the current recursion
// list temporary result set
// result the final result
func backtrack(nums []int, visited []bool, list []int, result *[][]int) {
	// The temporary result is a full permutation only when its length matches the input set
	if len(list) == len(nums) {
		subResult := make([]int, len(list))
		copy(subResult, list)
		*result = append(*result, subResult)
	}
	for i := 0; i < len(nums); i++ {
		// Skip elements that have already been added
		if visited[i] {
			continue
		}
        // If the previous element is the same as the current one and has not been visited, skip
		if i != 0 && nums[i] == nums[i-1] && !visited[i-1] {
			continue
		}
		list = append(list, nums[i])
		visited[i] = true
		backtrack(nums, visited, list, result)
		visited[i] = false
		list = list[0 : len(list)-1]
	}
}
```

## Exercises

- [ ] [subsets](https://leetcode-cn.com/problems/subsets/)
- [ ] [subsets-ii](https://leetcode-cn.com/problems/subsets-ii/)
- [ ] [permutations](https://leetcode-cn.com/problems/permutations/)
- [ ] [permutations-ii](https://leetcode-cn.com/problems/permutations-ii/)

Challenge problems

- [ ] [combination-sum](https://leetcode-cn.com/problems/combination-sum/)
- [ ] [letter-combinations-of-a-phone-number](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)
- [ ] [palindrome-partitioning](https://leetcode-cn.com/problems/palindrome-partitioning/)
- [ ] [restore-ip-addresses](https://leetcode-cn.com/problems/restore-ip-addresses/)
- [ ] [permutations](https://leetcode-cn.com/problems/permutations/)
