# Binary Search

## Binary Search Template

Given a **sorted array** and a target value, find the index of the first/last/any occurrence; return -1 if it does not appear.

Four key elements of the template:

- 1. Initialization: start=0, end=len-1
- 2. Loop exit condition: start + 1 < end
- 3. Compare the midpoint with the target: A[mid] ==, <, > target
- 4. Check whether the final two elements match: A[start], A[end] ? target

Time complexity is O(logn). The typical use case is searching in a sorted array.

Typical example:

[binary-search](https://leetcode-cn.com/problems/binary-search/)

> Given an array `nums` of n elements sorted in ascending order, and a target value `target`, write a function that searches for `target` in `nums`. If the target value exists, return its index; otherwise return -1.

```go
// The most commonly used binary search template
func search(nums []int, target int) int {
    // 1. Initialize start and end
    start := 0
    end := len(nums) - 1
    // 2. Handle the for loop
    for start+1 < end {
        mid := start + (end-start)/2
        // 3. Compare a[mid] with the target value
        if nums[mid] == target {
            end = mid
        } else if nums[mid] < target {
            start = mid
        } else if nums[mid] > target {
            end = mid
        }
    }
    // 4. Two elements remain at the end; check them manually
    if nums[start] == target {
        return start
    }
    if nums[end] == target {
        return end
    }
    return -1
}
```

Most binary search problems can use this template, then add a little special logic.

There are also some other binary search templates, as shown below. In most scenarios, template #3 can solve the problem, and it can also find the first/last occurrence position, making it more widely applicable.

![binary_search_template](https://cdn.jsdelivr.net/gh/greyireland/algorithm-pattern@master/images/binary_search_template.png)

So just use template #3. For a detailed comparison, see this article: [Binary Search Template](https://leetcode-cn.com/explore/learn/card/binary-search/212/template-analysis/847/)

If it is the simplest binary search—where you don't need to find the first or last position, or there are no duplicate elements—you can use template #1, which makes the code more concise.

```go
// More convenient when searching without duplicate elements
func search(nums []int, target int) int {
    start := 0
    end := len(nums) - 1
    for start <= end {
        mid := start + (end-start)/2
        if nums[mid] == target {
            return mid
        } else if nums[mid] < target {
            start = mid+1
        } else if nums[mid] > target {
            end = mid-1
        }
    }
    // If not found, start is the index of the first element greater than target
    // For binary search in a B+ tree structure, you can return start
    // so you can continue searching into child nodes, e.g.: node:=node.Children[start]
    return -1
}
```

## Common Problems

### [search-for-range](https://www.lintcode.com/problem/search-for-a-range/description)

> Given a sorted array containing n integers, find the starting and ending positions of a given target value `target`.
> If the target value is not in the array, return `[-1, -1]`.

Idea: the core point is to find the index of the first `target` and the index of the last `target`, so use two binary searches to find the first and last positions respectively.

```go
func searchRange (A []int, target int) []int {
    if len(A) == 0 {
        return []int{-1, -1}
    }
    result := make([]int, 2)
    start := 0
    end := len(A) - 1
    for start+1 < end {
        mid := start + (end-start)/2
        if A[mid] > target {
            end = mid
        } else if A[mid] < target {
            start = mid
        } else {
            // If equal, keep searching to the left to find the first occurrence of the target value
            end = mid
        }
    }
    // Search for the left index
    if A[start] == target {
        result[0] = start
    } else if A[end] == target {
        result[0] = end
    } else {
        result[0] = -1
        result[1] = -1
        return result
    }
    start = 0
    end = len(A) - 1
    for start+1 < end {
        mid := start + (end-start)/2
        if A[mid] > target {
            end = mid
        } else if A[mid] < target {
            start = mid
        } else {
            // If equal, keep searching to the right to find the last occurrence of the target value
            start = mid
        }
    }
    // Search for the right index
    if A[end] == target {
        result[1] = end
    } else if A[start] == target {
        result[1] = start
    } else {
        result[0] = -1
        result[1] = -1
        return result
    }
    return result
}
```

### [search-insert-position](https://leetcode-cn.com/problems/search-insert-position/)

> Given a sorted array and a target value, find the target value in the array and return its index. If the target value does not exist in the array, return the position where it would be inserted in order.

```go
func searchInsert(nums []int, target int) int {
    // Idea: find the position of the first element >= target
    start := 0
    end := len(nums) - 1
    for start+1 < end {
        mid := start + (end-start)/2
        if nums[mid] == target {
            // Mark the starting position
            start = mid
        } else if nums[mid] > target {
            end = mid
        } else {
            start = mid
        }
    }
    if nums[start] >= target {
        return start
    } else if nums[end] >= target {
        return end
    } else if nums[end] < target { // The target value is greater than all values
        return end + 1
    }
    return 0
}
```

### [search-a-2d-matrix](https://leetcode-cn.com/problems/search-a-2d-matrix/)

> Write an efficient algorithm to determine whether a target value exists in an m x n matrix. The matrix has the following properties:
>
> - Integers in each row are sorted in ascending order from left to right.
> - The first integer of each row is greater than the last integer of the previous row.

```go
func searchMatrix(matrix [][]int, target int) bool {
    // Idea: convert the 2D array into a 1D array and perform binary search
    if len(matrix) == 0 || len(matrix[0]) == 0 {
        return false
    }
    row := len(matrix)
    col := len(matrix[0])
    start := 0
    end := row*col - 1
    for start+1 < end {
        mid := start + (end-start)/2
        // Get the corresponding value in the 2D array
        val := matrix[mid/col][mid%col]
        if val > target {
            end = mid
        } else if val < target {
            start = mid
        } else {
            return true
        }
    }
    if matrix[start/col][start%col] == target || matrix[end/col][end%col] == target{
        return true
    }
    return false
}
```

### [first-bad-version](https://leetcode-cn.com/problems/first-bad-version/)

> Suppose you have n versions [1, 2, ..., n], and you want to find the first bad version that causes all the following versions to be bad.
> You can call the `bool isBadVersion(version)` interface to determine whether version number `version` failed in unit testing. Implement a function to find the first bad version. You should minimize the number of calls to the API.

```go
func firstBadVersion(n int) int {
    // Idea: binary search
    start := 1
    end := n
    for start+1 < end {
        mid := start + (end - start)/2
        if isBadVersion(mid) {
            end = mid
        } else if isBadVersion(mid) == false {
            start = mid
        }
    }
    if isBadVersion(start) {
        return start
    }
    return end
}
```

### [find-minimum-in-rotated-sorted-array](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

> Suppose an array sorted in ascending order is rotated at some pivot unknown in advance (for example, the array [0,1,2,4,5,6,7] might become [4,5,6,7,0,1,2]).
> Find the minimum element.

```go
func findMin(nums []int) int {
    // Idea: use the last value as the target, then move left, and finally compare the values of start and end
    if len(nums) == 0 {
        return -1
    }
    start := 0
    end := len(nums) - 1

    for start+1 < end {
        mid := start + (end-start)/2
        // The last element value is the target
        if nums[mid] <= nums[end] {
            end = mid
        } else {
            start = mid
        }
    }
    if nums[start] > nums[end] {
        return nums[end]
    }
    return nums[start]
}
```

### [find-minimum-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

> Suppose an array sorted in ascending order is rotated at some pivot unknown in advance
> (for example, the array [0,1,2,4,5,6,7] might become [4,5,6,7,0,1,2]).
> Find the minimum element. (May contain duplicate elements.)

```go
func findMin(nums []int) int {
    // Idea: skip duplicate elements, compare the mid value with the end value, and handle two cases
    if len(nums) == 0 {
        return -1
    }
    start := 0
    end := len(nums) - 1
    for start+1 < end {
        // Remove duplicate elements
        for start < end && nums[end] == nums[end-1] {
            end--
        }
        for start < end && nums[start] == nums[start+1] {
            start++
        }
        mid := start + (end-start)/2
        // Compare the middle element with the last element (to determine whether the midpoint falls in the left ascending region or the right ascending region)
        if nums[mid] <= nums[end] {
            end = mid
        } else {
            start = mid
        }
    }
    if nums[start] > nums[end] {
        return nums[end]
    }
    return nums[start]
}
```

### [search-in-rotated-sorted-array](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

> Suppose an array sorted in ascending order is rotated at some pivot unknown in advance.
> (For example, the array [0,1,2,4,5,6,7] might become [4,5,6,7,0,1,2].)
> Search for a given target value; if the target value exists in the array, return its index, otherwise return -1.
> You may assume there are no duplicate elements in the array.

```go
func search(nums []int, target int) int {
    // Idea: two ascending lines, four cases to consider
    if len(nums) == 0 {
        return -1
    }
    start := 0
    end := len(nums) - 1
    for start+1 < end {
        mid := start + (end-start)/2
        // Return directly if equal
        if nums[mid] == target {
            return mid
        }
        // Determine which interval it is in, which can be divided into four cases
        if nums[start] < nums[mid] {
            if nums[start] <= target && target <= nums[mid] {
                end = mid
            } else {
                start = mid
            }
        } else if nums[end] > nums[mid] {
            if nums[end] >= target && nums[mid] <= target {
                start = mid
            } else {
                end = mid
            }
        }
    }
    if nums[start] == target {
        return start
    } else if nums[end] == target {
        return end
    }
    return -1
}
```

Note:

> In an interview, you can draw a diagram to help explain; talking abstractly can easily confuse everyone.

### [search-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)

> Suppose an array sorted in ascending order is rotated at some pivot unknown in advance.
> (For example, the array [0,0,1,2,2,5,6] might become [2,5,6,0,0,1,2].)
> Write a function to determine whether a given target value exists in the array. Return true if it exists, otherwise return false. (May contain duplicate elements.)

```go
func search(nums []int, target int) bool {
    // Idea: two ascending lines, four cases to consider, and handle duplicate numbers
    if len(nums) == 0 {
        return false
    }
    start := 0
    end := len(nums) - 1
    for start+1 < end {
        // Handle duplicate numbers
        for start < end && nums[start] == nums[start+1] {
            start++
        }
        for start < end && nums[end] == nums[end-1] {
            end--
        }
        mid := start + (end-start)/2
        // Return directly if equal
        if nums[mid] == target {
            return true
        }
        // Determine which interval it is in, which can be divided into four cases
        if nums[start] < nums[mid] {
            if nums[start] <= target && target <= nums[mid] {
                end = mid
            } else {
                start = mid
            }
        } else if nums[end] > nums[mid] {
            if nums[end] >= target && nums[mid] <= target {
                start = mid
            } else {
                end = mid
            }
        }
    }
    if nums[start] == target || nums[end] == target {
        return true
    }
    return false
}
```

## Summary

The four core elements of binary search (memorize & understand):

- 1. Initialization: start=0, end=len-1
- 2. Loop exit condition: start + 1 < end
- 3. Compare the midpoint with the target: A[mid] ==, <, > target
- 4. Check whether the final two elements match: A[start], A[end] ? target

## Exercises

- [ ] [search-for-range](https://www.lintcode.com/problem/search-for-a-range/description)
- [ ] [search-insert-position](https://leetcode-cn.com/problems/search-insert-position/)
- [ ] [search-a-2d-matrix](https://leetcode-cn.com/problems/search-a-2d-matrix/)
- [ ] [first-bad-version](https://leetcode-cn.com/problems/first-bad-version/)
- [ ] [find-minimum-in-rotated-sorted-array](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)
- [ ] [find-minimum-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)
- [ ] [search-in-rotated-sorted-array](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)
- [ ] [search-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)
