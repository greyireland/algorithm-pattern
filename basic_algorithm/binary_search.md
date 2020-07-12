# 二分搜索

## 二分搜索模板

给一个**有序数组**和目标值，找第一次/最后一次/任何一次出现的索引，如果没有出现返回-1

模板四点要素

- 1、初始化：start=0、end=len-1
- 2、循环退出条件：start + 1 < end
- 3、比较中点和目标值：A[mid] ==、 <、> target
- 4、判断最后两个元素是否符合：A[start]、A[end] ? target

时间复杂度 O(logn)，使用场景一般是有序数组的查找

典型示例

[binary-search](https://leetcode-cn.com/problems/binary-search/)

> 给定一个  n  个元素有序的（升序）整型数组  nums 和一个目标值  target  ，写一个函数搜索  nums  中的 target，如果目标值存在返回下标，否则返回 -1。

```c++
// 二分搜索最常用模板
int search(vector<int> &nums, int target) {
    // 1、初始化start、end
    auto start = 0;
    auto end = nums.size() - 1;
    // 2、处理for循环
    while (start + 1 < end) {
        auto mid = start + (end - start) / 2;
        // 3、比较a[mid]和target值
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            start = mid;
        } else {
            end = mid;
        }
    }
    // 4、最后剩下两个元素，手动判断
    if (nums[start] == target) {
        return start;
    }
    if (nums[end] == target) {
        return end;
    }
    return -1;
}
```

大部分二分查找类的题目都可以用这个模板，然后做一点特殊逻辑即可

另外二分查找还有一些其他模板如下图，大部分场景模板#3 都能解决问题，而且还能找第一次/最后一次出现的位置，应用更加广泛

![binary_search_template](https://img.fuiboom.com/img/binary_search_template.png)

所以用模板#3 就对了，详细的对比可以这边文章介绍：[二分搜索模板](https://leetcode-cn.com/explore/learn/card/binary-search/212/template-analysis/847/)

如果是最简单的二分搜索，不需要找第一个、最后一个位置、或者是没有重复元素，可以使用模板#1，代码更简洁

```c++
// 无重复元素搜索时，更方便
int search(vector<int> &nums, int target) {
    auto start = 0;
    auto end = nums.size() - 1;
    while (start <= end) {
        auto mid = start + (end - start) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            start = mid + 1;
        } else {
            end = mid - 1;
        }
    }
    // 如果找不到，start 是第一个大于target的索引
    // 如果在B+树结构里面二分搜索，可以return start
    // 这样可以继续向子节点搜索，如：node = node.Children[start]
    return -1
}
```

## 常见题目

### [search-for-range](https://www.lintcode.com/problem/search-for-a-range/description)

> 给定一个包含 n 个整数的排序数组，找出给定目标值 target 的起始和结束位置。
> 如果目标值不在数组中，则返回`[-1, -1]`

思路：核心点就是找第一个 target 的索引，和最后一个 target 的索引，所以用两次二分搜索分别找第一次和最后一次的位置

```c++
// 两次搜索，一次相等时往左一次往右以找出左右边界
vector<int> searchRange(vector<int> &A, int target) {
    vector<int> ret = {-1, -1};
    if (A.empty()) {
        return ret;
    }
    int begin = 0;
    int end = A.size();
    while (begin + 1 < end) {
        auto mid = begin + (end - begin) / 2;
        if (A[mid] < target) {
            begin = mid;
        } else {
            end = mid;
        }
    }
    if (A[begin] == target) {
        ret[0] = begin;
    } else if (A[end] == target) {
        ret[0] = end;
    } else {
        return ret;
    }
    begin = 0;
    end = A.size();
    while (begin + 1 < end) {
        auto mid = begin + (end - begin) / 2;
        if (A[mid] <= target) {
            begin = mid;
        } else {
            end = mid;
        }
    }
    if (A[begin] == target) {
        ret[1] = begin;
    } else if (A[end] == target) {
        ret[1] = end;
    } else {
        return ret;
    }
    return ret;
}
```

### [search-insert-position](https://leetcode-cn.com/problems/search-insert-position/)

> 给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

```c++
int searchInsert(vector<int>& nums, int target) {
    if (nums.empty()) {
        return 0;
    }

    int begin = 0;
    int end = nums.size() -1;
    while (begin + 1 < end) {
        auto mid = begin + (end - begin) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            begin = mid;
        } else {
            end = mid;
        }
    }
    if (nums[begin] >= target) {
        return begin;
    } else if (nums[end] >= target) {
        return end;
    } else {
        return end + 1;
    } 
}
```

### [search-a-2d-matrix](https://leetcode-cn.com/problems/search-a-2d-matrix/)

> 编写一个高效的算法来判断  m x n  矩阵中，是否存在一个目标值。该矩阵具有如下特性：
>
> - 每行中的整数从左到右按升序排列。
> - 每行的第一个整数大于前一行的最后一个整数。

```c++
bool searchMatrix(vector<vector<int>>& matrix, int target) {
    if (matrix.empty() ||  matrix[0].empty()) {
        return false;
    }

    int begin = 0;
    int end = matrix.size() - 1;
    while (begin + 1 < end) {
        auto mid = begin + (end - begin) / 2;
        const auto &val = matrix[mid][0];
        if (val == target) {
            return true;
        } else if (val < target) {
            begin = mid;
        } else {
            end = mid;
        }
    }

    if (matrix[begin][0] == target || matrix[end][0] == target) {
        return true;
    }
    if (matrix[begin][0] > target || matrix[end][matrix[end].size()- 1] < target) {
        return false;
    }
    const auto &row = matrix[end][0] < target ? matrix[end] : matrix[begin];
    begin = 0;
    end = row.size() - 1;
    while (begin + 1 < end) {
        auto mid = begin + (end - begin) / 2;
        if (row[mid] == target) {
            return true;
        } else if (row[mid] < target) {
            begin = mid;
        } else {
            end = mid;
        }
    }
    return row[begin] == target || row[end] == target;
}
```

### [first-bad-version](https://leetcode-cn.com/problems/first-bad-version/)

> 假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。
> 你可以通过调用  bool isBadVersion(version)  接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。

```c++
int firstBadVersion(int n) {
    int begin = 1;
    int end = n;
    while (begin + 1 < end) {
        auto mid = begin + (end - begin) / 2;
        if (isBadVersion(mid)) {
            end = mid;
        } else {
            begin = mid;
        }
    }
    return isBadVersion(begin) ? begin : end;
}
```

### [find-minimum-in-rotated-sorted-array](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转( 例如，数组  [0,1,2,4,5,6,7] 可能变为  [4,5,6,7,0,1,2] )。
> 请找出其中最小的元素。

```c++
int findMin(vector<int>& nums) {
    // 最后一个值作为target，以确定是否旋转
    int begin = 0;
    int end = nums.size() - 1;
    while (begin + 1 < end) {
        auto mid = begin + (end - begin) / 2;
        if (nums[mid] <= nums[end]) {
            end = mid;
        } else {
            begin = mid;
        }
    }
    return nums[begin] > nums[end] ? nums[end] : nums[begin];
}
```

### [find-minimum-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转
> ( 例如，数组  [0,1,2,4,5,6,7] 可能变为  [4,5,6,7,0,1,2] )。
> 请找出其中最小的元素。(包含重复元素)

```c++
int findMin(vector<int> &nums) {
    if (nums.empty()) {
        return -1;
    }
    auto left = 0;
    auto right = nums.size() - 1;
    while (left + 1 < right) {
        while (left < right && nums[right] == nums[right - 1]) {
            --right;
        }
        while (left < right && nums[left] == nums[left + 1]) {
            ++left;
        }
        auto mid = left + (right - left) / 2;
        if (nums[mid] <= nums[right]) {
            right = mid;
        } else {
            left = mid;
        }
    }
    return (nums[left] >= nums[right]) ? nums[right] : nums[left];
}
```

### [search-in-rotated-sorted-array](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转。
> ( 例如，数组  [0,1,2,4,5,6,7]  可能变为  [4,5,6,7,0,1,2] )。
> 搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回  -1 。
> 你可以假设数组中不存在重复的元素。

```c++
int search(vector<int> &nums, int target) {
    if (nums.empty()) {
        return -1;
    }
    auto left = 0;
    auto right = nums.size() - 1;
    while (left + 1 < right) {
        auto mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        }
        if (nums[left] < nums[mid]) {
            if (nums[left] <= target && target <= nums[mid]) {
                right = mid;
            } else {
                left = mid;
            }
        } else if (nums[mid] < nums[right]) {
            // 这个判断是必须的！当只有两个数的时候，可能两个都不满足！
            if (target >= nums[mid] && nums[right] >= target) {
                right = mid;
            } else {
                left = mid;
            }
        }
    }
    if (nums[left] == target) {
        return left;
    } else if (nums[right] == target) {
        return right;
    } else {
        return -1;
    }
}
```

注意点

> 面试时，可以直接画图进行辅助说明，空讲很容易让大家都比较蒙圈

### [search-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转。
> ( 例如，数组  [0,0,1,2,2,5,6]  可能变为  [2,5,6,0,0,1,2] )。
> 编写一个函数来判断给定的目标值是否存在于数组中。若存在返回  true，否则返回  false。(包含重复元素)

```c++
bool search(vector<int>& nums, int target) {
    if (nums.empty()) {
        return false;
    }
    auto left = 0;
    auto right = nums.size() - 1;
    while (left + 1 < right) {
        while (left < right && nums[left] == nums[left + 1]) {
            ++left;
        }
        while (left < right && nums[right] == nums[right - 1]) {
            --right;
        }
        auto mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return true;
        }
        if (nums[left] < nums[mid]) {
            if (nums[left] <= target && target <= nums[mid]) {
                right = mid;
            } else {
                left = mid;
            }
        } else if (nums[mid] < nums[right]) {
            if (nums[mid] <= target && nums[right] >= target) {
                left = mid;
            } else {
                right = mid;
            }
        }
    }
    return nums[left] == target || nums[right] == target;
}
```

## 总结

二分搜索核心四点要素（必背&理解）

- 1、初始化：start=0、end=len-1
- 2、循环退出条件：start + 1 < end
- 3、比较中点和目标值：A[mid] ==、 <、> target
- 4、判断最后两个元素是否符合：A[start]、A[end] ? target

## 练习题

- [ ] [search-for-range](https://www.lintcode.com/problem/search-for-a-range/description)
- [ ] [search-insert-position](https://leetcode-cn.com/problems/search-insert-position/)
- [ ] [search-a-2d-matrix](https://leetcode-cn.com/problems/search-a-2d-matrix/)
- [ ] [first-bad-version](https://leetcode-cn.com/problems/first-bad-version/)
- [ ] [find-minimum-in-rotated-sorted-array](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)
- [ ] [find-minimum-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)
- [ ] [search-in-rotated-sorted-array](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)
- [ ] [search-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)
