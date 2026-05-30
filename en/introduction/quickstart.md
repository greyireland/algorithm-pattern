# Quick Start

## Data Structures and Algorithms

A data structure is a way of representing data. Linked lists, binary trees, stacks, queues, and so on are all ways a piece of data is represented in memory.
An algorithm is a general template or approach for solving a problem. Most data structures have a set of general-purpose algorithm templates, so mastering these general templates is enough to solve a wide variety of algorithm problems.

The following chapters will cover various data structures, basic algorithm templates, and some advanced algorithm templates by topic. Each topic includes several classic practice problems. After completing all the practice problems, you will gain new insights and understanding of data structures and algorithms.

Let's start with two algorithm problems to get a feel for it~

Example 1

[strStr](https://leetcode-cn.com/problems/implement-strstr/)

> Given a haystack string and a needle string, find the index of the first occurrence (starting from 0) of needle in haystack. If it doesn't exist, return -1.

Idea: The key point is to iterate over the characters of the given string and check whether the substring starting at the current character equals the target string.

```go
func strStr(haystack string, needle string) int {
    if len(needle) == 0 {
        return 0
    }
    var i, j int
    // i does not need to go up to len-1
    for i = 0; i < len(haystack)-len(needle)+1; i++ {
        for j = 0; j < len(needle); j++ {
            if haystack[i+j] != needle[j] {
                break
            }
        }
        // Check whether the string lengths are equal
        if len(needle) == j {
            return i
        }
    }
    return -1
}
```

Things to note

- During the loop, i does not need to go up to len-1.
- If the target string is found, then len(needle)==j.

Example 2

[subsets](https://leetcode-cn.com/problems/subsets/)

> Given an integer array nums with no duplicate elements, return all possible subsets (the power set) of the array.

Idea: This is a classic problem that applies backtracking. In short, it exhaustively enumerates all possibilities. The algorithm template is as follows:

```go
result = []
func backtrack(choice list, path):
    if end condition is met:
        result.add(path)
        return
    for choice in choice list:
        make a choice
        backtrack(choice list, path)
        undo the choice
```

By continuously making choices and undoing choices, we exhaust all possibilities, and finally return the results that satisfy the conditions.

Solution code

```go
func subsets(nums []int) [][]int {
    // Store the final result
    result := make([][]int, 0)
    // Store the intermediate result
    list := make([]int, 0)
    backtrack(nums, 0, list, &result)
    return result
}

// nums the given set
// pos the index of the element to add to the set next time
// list the temporary result set (needs to be copied and saved each time)
// result the final result
func backtrack(nums []int, pos int, list []int, result *[][]int) {
    // Copy the temporary result and save it to the final result
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

Note: Several classic backtracking problems will be discussed in depth later. If you don't quite understand this now, you can skip it for the moment.

## Interview Tips

Most of the time, we practice algorithm problems to prepare for interviews, so there are a few things to keep in mind during an interview.

- Quickly identify the knowledge point of the problem and find the **general template** for that knowledge point; you may need to apply **special handling for special cases** depending on the problem.
- First aim in a direction that solves the problem! **Propose a feasible solution first**, not the optimal one! Solve it first, then optimize!
- Keep your coding style consistent and be familiar with the code conventions of each language.
  - Keep names as concise and clear as possible; avoid naming with numbers such as i1, node1, a1, b2.
- Summary of common mistakes
  - When accessing an index, do not go out of bounds.
  - The nil value problem causes a run time error.

## Exercises

- [ ] [strStr](https://leetcode-cn.com/problems/implement-strstr/)
- [ ] [subsets](https://leetcode-cn.com/problems/subsets/)
