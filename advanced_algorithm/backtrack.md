# 回溯法

## 背景

回溯法（backtrack）常用于遍历列表所有子集，是 DFS 深度搜索一种，一般用于全排列，穷尽所有可能，遍历的过程实际上是一个决策树的遍历过程。时间复杂度一般 O(N!)，它不像动态规划存在重叠子问题可以优化，回溯算法就是纯暴力穷举，复杂度一般都很高。

## 模板

```c++
vector<T> result;
void backtrack(选择列表,路径):
    if 满足结束条件:
        result.push_back(路径);
        return
    for 选择 in 选择列表:
        做选择
        backtrack(选择列表,路径)
        撤销选择
```

核心就是从选择列表里做一个选择，然后一直递归往下搜索答案，如果遇到路径不通，就返回来撤销这次选择。

## 示例

### [subsets](https://leetcode-cn.com/problems/subsets/)

> 给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

遍历过程

![image.png](https://img.fuiboom.com/img/backtrack.png)

```c++
vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> result;
    vector<int> track;
    backtrack(nums, 0, track, result);
    return result;
}

void backtrack(const vector<int> &nums, int pos, vector<int> &track, vector<vector<int>> &result) {
    // 插入当前组合
    result.push_back(track);
    for (int i = pos; i < nums.size(); ++i) {
        // 每个值，都有两种可能，包含和不包含
        // 先压入
        track.push_back(nums[i]);
        // 递归处理包含当前值的情况
        backtrack(nums, i + 1, track, result);
        // 弹出，向后遍历，处理不包含的情况
        track.pop_back();
    }
}
```

### [subsets-ii](https://leetcode-cn.com/problems/subsets-ii/)

> 给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。说明：解集不能包含重复的子集。

```c++
// 对数据进行排序来判断重复元素
vector<vector<int>> subsetsWithDup(vector<int> &nums) {
    vector<vector<int>> result;
    vector<int> track;
    sort(nums.begin(), nums.end());
    backtrack(nums, 0, track, result);
    return result;
}

void backtrack(const vector<int> &nums, int pos, vector<int> &track, vector<vector<int>> &result) {
    result.push_back(track);
    for (int i = pos; i < nums.size(); ++i) {
        if (i != pos && nums[i] == nums[i - 1]) {
            continue;
        }
        track.push_back(nums[i]);
        backtrack(nums, i + 1, track, result);
        track.pop_back();
    }
}
```

### [permutations](https://leetcode-cn.com/problems/permutations/)

> 给定一个   没有重复   数字的序列，返回其所有可能的全排列。

思路：需要记录已经选择过的元素，满足条件的结果才进行返回

```c++
/*
 * 这个问题可以看作有 n 个排列成一行的空格，从左往右依此填入题目给定的 n 个数，每个数只能使用一次
 *
 * 回溯法
 * 不同于上面的子集问题，撤销选择时弹出了事，这里撤销后还要留待后续组合使用
 * 并且需要检查数字是否已经填入过
 *
 * 思路：
 * backtrack(fillingPos, result)，fillingPos为当前填入的位置
 * 若fillingPos == n，则已经完成排列
 * 否则，填入"未填入过"的数字，递归处理下一个位置，最后撤销当前选择，尝试以其他"未填入过"的数字来填入fillingPos
 *
 * 优化：
 * 不使用额外的数组来维护已经填入过的数字，降低空间复杂度
 * 对于nums、当前位置fillingPos
 * [0, fillingPos - 1]——已填入过的数字
 * [fillingPos, n - 1]——未填入过的数字
 * 每次选择时把nums[i]与nums[fillingPos]进行交换
 * 撤销选择时把nums[i]与nums[fillingPos]再交换一次即可
 *
 * 既然 [fillingPos, n - 1] 为未填入的数字
 * 偷个鸡，撤销选择时不交换，直接 ++i 可否？
 * 并不能
 * 因为原本的 nums[i] 填入 fillingPos 这一可能性已经处理过了
 * 如果不交换回来，for循环遍历的时候会再用到同一个数字！
 */
vector<vector<int>> permute(vector<int> &nums) {
    vector<vector<int>> result;
    backtrack(result, nums, 0, nums.size());
    return result;
}

void backtrack(vector<vector<int>> &result, vector<int> &nums, int fillingPos, int len) {
    if (fillingPos == len) {
        // n个数字都已排列完，保存并退出
        result.emplace_back(nums);
        return;
    }
    for (int i = fillingPos; i < len; ++i) {
        // 把nums[i]填入到当前位置
        swap(nums[i], nums[fillingPos]);
        backtrack(result, nums, fillingPos + 1, len);
        // 把顺序恢复回去
        swap(nums[i], nums[fillingPos]);
    }
}
```

### [permutations-ii](https://leetcode-cn.com/problems/permutations-ii/)

> 给定一个可包含重复数字的序列，返回所有不重复的全排列。

```c++
vector<vector<int>> permuteUnique(vector<int> &nums) {
    vector<vector<int>> result;
    vector<int> current;
    vector<bool> visited(nums.size());
    sort(nums.begin(), nums.end());
    backtrack(nums, nums.size(), visited, current, result);
    return result;
}

void backtrack(vector<int> &nums, int len, vector<bool> &visited,
               vector<int> &current, vector<vector<int>> &result) {
    if (current.size() == nums.size()) {
        result.emplace_back(current);
        return;
    }
    for (int i = 0; i < nums.size(); ++i) {
        // 已经在组合中
        if (visited[i]) {
            continue;
        }
        // 与上一个元素相同，并且没有访问过就跳过
        // 没访问过？
        // 重复元素，只有第一个元素考虑排列组合，后面的不再考虑以免重复
        // 一旦第一个重复的元素被pop并设置visited为false，则其所有排列组合都已考虑完毕
        // 跳过后续的重复元素
        if (i > 0 && nums[i] == nums[i - 1] && !visited[i - 1]) {
            continue;
        }
        current.emplace_back(nums[i]);
        visited[i] = true;
        backtrack(nums, len, visited, current, result);
        visited[i] = false;
        current.pop_back();
    }
}
```

## 练习

- [ ] [subsets](https://leetcode-cn.com/problems/subsets/)
- [ ] [subsets-ii](https://leetcode-cn.com/problems/subsets-ii/)
- [ ] [permutations](https://leetcode-cn.com/problems/permutations/)
- [ ] [permutations-ii](https://leetcode-cn.com/problems/permutations-ii/)

挑战题目

- [ ] [combination-sum](https://leetcode-cn.com/problems/combination-sum/)
- [ ] [letter-combinations-of-a-phone-number](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)
- [ ] [palindrome-partitioning](https://leetcode-cn.com/problems/palindrome-partitioning/)
- [ ] [restore-ip-addresses](https://leetcode-cn.com/problems/restore-ip-addresses/)
- [ ] [permutations](https://leetcode-cn.com/problems/permutations/)
