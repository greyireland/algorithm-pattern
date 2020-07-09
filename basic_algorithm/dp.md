# 动态规划

## 背景

先从一道题目开始~

如题  [triangle](https://leetcode-cn.com/problems/triangle/)

> 给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

例如，给定三角形：

```text
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```

自顶向下的最小路径和为  11（即，2 + 3 + 5 + 1 = 11）。

使用 DFS（遍历 或者 分治法）

遍历

![image.png](https://img.fuiboom.com/img/dp_triangle.png)

分治法

![image.png](https://img.fuiboom.com/img/dp_dc.png)

优化 DFS，缓存已经被计算的值（称为：记忆化搜索 本质上：动态规划）

![image.png](https://img.fuiboom.com/img/dp_memory_search.png)

动态规划就是把大问题变成小问题，并解决了小问题重复计算的方法称为动态规划

动态规划和 DFS 区别

- 二叉树 子问题是没有交集，所以大部分二叉树都用递归或者分治法，即 DFS，就可以解决
- 像 triangle 这种是有重复走的情况，**子问题是有交集**，所以可以用动态规划来解决

动态规划，自底向上

```c++
minimumTotal(vector<vector<int>>& triangle) {
    if (triangle.empty() || triangle[0].empty()) {
        return 0;
    }
    auto rowNum = triangle.size();
    auto cache = triangle;
    for (int row = rowNum - 2; row >= 0; --row) {
        for (int col = 0; col < triangle[row].size(); ++col) {
            cache[row][col] = min(cache[row + 1][col], cache[row + 1][col + 1]) + triangle[row][col];
        }
    }
    return cache[0][0];
}
```

动态规划，自顶向下

```c++
// 测试用例：
// [
// [2],
// [3,4],
// [6,5,7],
// [4,1,8,3]
// ]
int minimumTotal(vector<vector<int>>& triangle) {
    if (triangle.empty() || triangle[0].empty()) {
        return 0;
    }
    auto cache = triangle;
    for (int row = 1; row < triangle.size(); ++row) {
        int colNum = triangle[row].size();
        int col = 0;
        for (; col < colNum; ++col) {
            if (col == 0) {
                cache[row][col] = cache[row-1][col];
            } else if (col >= triangle[row - 1].size()) {
                cache[row][col] = cache[row - 1][col - 1];
            } else {
                cache[row][col] = min(cache[row-1][col], cache[row-1][col-1]);
            }
            cache[row][col] += triangle[row][col];
        }
    }
    auto min = cache.back()[0];
    for (const auto &item : cache.back()) {
        min = std::min(min, item);
    }
    return min;
}
```

## 递归和动规关系

递归是一种程序的实现方式：函数的自我调用

```c++
Function(x) {
	...
	Funciton(x-1);
	...
}
```

动态规划：是一种解决问 题的思想，大规模问题的结果，是由小规模问 题的结果运算得来的。动态规划可用递归来实现(Memorization Search)

## 使用场景

满足两个条件

- 满足以下条件之一
  - 求最大/最小值（Maximum/Minimum ）
  - 求是否可行（Yes/No ）
  - 求可行个数（Count(\*) ）
- 满足不能排序或者交换（Can not sort / swap ）

如题：[longest-consecutive-sequence](https://leetcode-cn.com/problems/longest-consecutive-sequence/)  位置可以交换，所以不用动态规划

## 四点要素

1. **状态 State**
   - 灵感，创造力，存储小规模问题的结果
2. 方程 Function
   - 状态之间的联系，怎么通过小的状态，来算大的状态
3. 初始化 Intialization
   - 最极限的小状态是什么, 起点
4. 答案 Answer
   - 最大的那个状态是什么，终点

## 常见四种类型

1. Matrix DP (10%)
1. Sequence (40%)
1. Two Sequences DP (40%)
1. Backpack (10%)

> 注意点
>
> - 贪心算法大多题目靠背答案，所以如果能用动态规划就尽量用动规，不用贪心算法

## 1、矩阵类型（10%）

### [minimum-path-sum](https://leetcode-cn.com/problems/minimum-path-sum/)

> 给定一个包含非负整数的  *m* x *n*  网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

思路：动态规划
1、state: f[x][y]从起点走到 x,y 的最短路径
2、function: f[x][y] = min(f[x-1][y], f[x][y-1]) + A[x][y]
3、intialize: f[0][0] = A[0][0]、f[i][0] = sum(0,0 -> i,0)、 f[0][i] = sum(0,0 -> 0,i)
4、answer: f[n-1][m-1]

```c++
int minPathSum(vector<vector<int>>& grid) {
    if (grid.empty() || grid[0].empty()) {
        return 0;
    }
    auto rowNum = grid.size();
    auto colNum = grid[0].size();
    for (int i = 1; i < rowNum; ++i) {
        grid[i][0] = grid[i-1][0] + grid[i][0];
    }
    for (int i = 1; i < colNum; ++i) {
        grid[0][i] = grid[0][i - 1] + grid[0][i];
    }
    for (int i = 1; i < rowNum; ++i) {
        for (int j = 1; j < colNum; ++j) {
            grid[i][j] = min(grid[i - 1][j], grid[i][j - 1]) + grid[i][j];
        }
    }
    return grid[rowNum - 1][colNum - 1];
}
```

### [unique-paths](https://leetcode-cn.com/problems/unique-paths/)

> 一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。
> 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。
> 问总共有多少条不同的路径？

```c++
int uniquePaths(int m, int n) {
    if (m == 0 || n == 0) {
        return 0;
    }
    vector<vector<int>> cache(m, vector<int>(n, 1));
    for (int i = 1; i < m; ++i) {
        for (int j = 1; j < n; ++j) {
            cache[i][j] = cache[i - 1][j] + cache[i][j - 1];
        }
    }
    return cache[m - 1][n - 1];
}
```

### [unique-paths-ii](https://leetcode-cn.com/problems/unique-paths-ii/)

> 一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。
> 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。
> 问总共有多少条不同的路径？
> 现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

```c++
int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
    if (obstacleGrid.empty() || obstacleGrid[0].empty() || obstacleGrid[0][0]) {
        return 0;
    }

    auto rowNum = obstacleGrid.size();
    auto colNum = obstacleGrid[0].size();
    auto &cache = obstacleGrid;
    obstacleGrid[0][0] = !obstacleGrid[0][0];
    for (int i = 1; i < colNum; ++i) {
        cache[0][i] = !cache[0][i] && cache[0][i - 1];
    }
    for (int i = 1; i < rowNum; ++i) {
        cache[i][0] = !cache[i][0] && cache[i - 1][0];
    }
    for (int i = 1; i < rowNum; ++i) {
        for (int j = 1; j < colNum; ++j) {
            if (cache[i][j]) {
                cache[i][j] = 0;
                continue;
            }
            cache[i][j] = cache[i - 1][j] + cache[i][j - 1];
        }
    }
    return cache[rowNum - 1][colNum - 1];
}
```

## 2、序列类型（40%）

### [climbing-stairs](https://leetcode-cn.com/problems/climbing-stairs/)

> 假设你正在爬楼梯。需要  *n*  阶你才能到达楼顶。

```c++
int climbStairs(int n) {
    vector<int> ret(n + 1, 1);
    for (int i = 2; i < n + 1; ++i) {
        ret[i] = ret[i - 1] + ret[i - 2];
    }
    return ret[n];
}
```

### [jump-game](https://leetcode-cn.com/problems/jump-game/)

> 给定一个非负整数数组，你最初位于数组的第一个位置。
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
> 判断你是否能够到达最后一个位置。

```c++
bool canJump(vector<int>& nums) {
    if (nums.empty()) {
        return true;
    }
    int farest = 0;
    for (int i = 0; i < nums.size(); ++i) {
        if (farest < i) {
            return false;
        }
        farest = max(farest, nums[i] + i);
        if (farest >= nums.size() - 1) {
            return true;
        }
    }
    return false;
}
```

### [jump-game-ii](https://leetcode-cn.com/problems/jump-game-ii/)

> 给定一个非负整数数组，你最初位于数组的第一个位置。
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
> 你的目标是使用最少的跳跃次数到达数组的最后一个位置。

```c++
int jump(vector<int>& nums) {
    int size = nums.size();
    vector<int> cache(size);
    cache[0] = 0;
    // cache[i + 1] >= cache[i]
    for (int i = 1, farest = 0; i < size; ++i) {
        while (farest < size && farest + nums[farest] < i) {
            ++farest;
        }
        // 假设你总是可以到达数组的最后一个位置。
        // 可以认定farest + nums[farest]一定会大于i
        cache[i] = cache[farest] + 1;
    }
    return cache.back();
}
```

### [palindrome-partitioning-ii](https://leetcode-cn.com/problems/palindrome-partitioning-ii/)

> 给定一个字符串 _s_，将 _s_ 分割成一些子串，使每个子串都是回文串。
> 返回符合要求的最少分割次数。

```c++
int minCut(string s) {
    if (s.size() <= 1) {
        return 0;
    }
    auto size = s.size();
    vector<int> cache(size);
    for (int i = 0; i < size; ++i) {
        cache[i] = i;
    }
    vector<vector<bool>> isPalindrome(size, vector<bool>(size));
    for (int right = 0; right < size; ++right) {
        for (int left = 0; left <= right; ++left) {
            // 如果头尾相同，可能为回文！
            // len < 3时，直接判断为回文
            // len >= 3时，判断里面一层，左右往里缩一格
            isPalindrome[left][right] = s[right] == s[left] && (right - left < 3 || isPalindrome[left + 1][right - 1]);
        }
    }
    for (int i = 1; i < size; ++i) {
        if (isPalindrome[0][i]) {
            cache[i] = 0;
            continue;
        }
        for (int j = 0; j < i; ++j) {
            if (isPalindrome[j + 1][i]) {
                cache[i] = min(cache[j] + 1, cache[i]);
            }
        }
    }
    return cache.back();
}
```

注意点

- 判断回文字符串时，可以提前用动态规划算好，减少时间复杂度

### [longest-increasing-subsequence](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

> 给定一个无序的整数数组，找到其中最长上升子序列的长度。

```c++
#pragma region 复杂度O(n^2)
int lengthOfLIS1(vector<int>& nums) {
    if (nums.size() < 2) {
        return nums.size();
    }

    vector<int> cache(nums.size());
    cache[0] = 1;
    for (int i = 1; i < nums.size(); ++i) {
        cache[i] = 1;
        for (int j = 0; j < i; ++j) {
            if (nums[j] < nums[i]) {
                cache[i] = max(cache[i], cache[j] + 1);
            }
        }
    }
    auto maxLen = cache[0];
    for (const auto &item : cache) {
        maxLen = max(maxLen, item);
    }
    return maxLen;
}
#pragma endregion

#pragma region 复杂度O(n log n)
// 题目给了提示，看到log n可以考虑往二分查找之类的上凑
// 思路：保存最大长度及其末尾节点，通过判断是否大于末尾节点来决定是否能够组成子序列
// 又，既然不能保证当前的"最大长度"会不会被其他组合反超
// 干脆把整个过程，每个长度都保存下来，构成单调栈
// 一方面通过栈顶来决定是否能够组合成更长的子序列
// 另一方面通过查找，找出比当前节点小的第一个末尾进行更新，以确保其他组合的机会
int lengthOfLIS(vector<int> &nums) {
    int size = nums.size();
    if (size < 2) {
        return size;
    }

    // tail[i]表示长度为i + 1的子序列的最小末尾值
    // tail必定单调递增，单调栈
    // 如果当前节点大于栈顶，即，比最长子序列的末尾还大，入栈（长度+1
    // 否则通过二分查找找到第一个比当前节点大的值，进行更新。子序列长度相同，末尾节点变小
    vector<int> tail;
    tail.push_back(nums.front());
    for (int i = 1; i < size; ++i) {
        if (nums[i] > tail.back()) {
            tail.push_back(nums[i]);
            continue;
        }
        auto left = 0;
        auto right = tail.size() - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (tail[mid] < nums[i]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        tail[left] = nums[i];
    }
    return tail.size();
}
#pragma endregion
```

### [word-break](https://leetcode-cn.com/problems/word-break/)

> 给定一个**非空**字符串  *s*  和一个包含**非空**单词列表的字典  *wordDict*，判定  *s*  是否可以被空格拆分为一个或多个在字典中出现的单词。

```c++
bool wordBreak(string s, vector<string> &wordDict) {
    unordered_set<string> wordDictSet{wordDict.begin(), wordDict.end()};
    vector<bool> dp(s.size() + 1);
    dp[0] = true;
    for (int i = 1; i <= s.size(); ++i) {
        for (int j = 0; j < i; ++j) {
            if (dp[j] && wordDictSet.find(s.substr(j, i - j)) != wordDictSet.end()) {
                dp[i] = true;
                break;
            }
        }
    }
    return dp.back();
}
```

小结

常见处理方式是给 0 位置占位，这样处理问题时一视同仁，初始化则在原来基础上 length+1，返回结果 f[n]

- 状态可以为前 i 个
- 初始化 length+1
- 取值 index=i-1
- 返回值：f[n]或者 f[m][n]

## Two Sequences DP（40%）

### [longest-common-subsequence](https://leetcode-cn.com/problems/longest-common-subsequence/)

> 给定两个字符串  text1 和  text2，返回这两个字符串的最长公共子序列。
> 一个字符串的   子序列   是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
> 例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。

```c++
vector<vector<int>> dp(text1.size() + 1, vector<int>(text2.size() + 1));
    for (int i = 1; i <= text1.size(); ++i) {
        for (int j = 1; j <= text2.size(); ++j) {
            if (text1[i - 1] == text2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    return dp.back().back();
}
```

注意点

- 从 1 开始遍历到最大长度
- 索引需要减一

### [edit-distance](https://leetcode-cn.com/problems/edit-distance/)

> 给你两个单词  word1 和  word2，请你计算出将  word1  转换成  word2 所使用的最少操作数  
> 你可以对一个单词进行如下三种操作：
> 插入一个字符
> 删除一个字符
> 替换一个字符

思路：和上题很类似，相等则不需要操作，否则取删除、插入、替换最小操作次数的值+1

```c++
int minDistance(string word1, string word2) {
    // 删除与插入操作是等价的，修改word1和修改word2也是等价的
    // 故，实际操作只有在三种：
    // 1. 在word1插入，dp[i][j] = dp[i][j - 1] + 1
    // 2. 在word2插入，dp[i][j] = dp[i - 1][j] + 1
    // 3. 在word1修改，dp[i][j] = dp[i - 1][j - 1] + 1
    vector<vector<int>> dp(word1.size(), vector<int>(word2.size()));
    for (int i = 0; i < word1.size(); ++i) {
        dp[i][0] = i;
    }
    for (int i = 0; i < word2.size(); ++i) {
        dp[0][i] = i;
    }
    for (int i = 1; i <= word1.size(); ++i) {
        for (int j = 1; j <= word2.size(); ++j) {
            if (word1[i - 1] == word2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {
                dp[i][j] = min(min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
            }
        }
    }
    return dp.back().back();
}
```

说明

> 另外一种做法：MAXLEN(a,b)-LCS(a,b)

## 零钱和背包（10%）

### [coin-change](https://leetcode-cn.com/problems/coin-change/)

> 给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回  -1。

思路：和其他 DP 不太一样，i 表示钱或者容量

```c++
int coinChange(vector<int>& coins, int amount) {
    // 初始化为amount + 1，如果最后大于amount说明无解
    // dp[i]表示金额为i时，所需最少硬币个数
    // dp[i] = min(dp[i], dp[i - coin] + 1)
    // dp[i - coin]，倒扣当前面额
    // 注意不要越界，i - coin >= 0
    vector<int> dp(amount + 1, amount + 1);
    dp[0] = 0;
    for (auto i = 1; i <= amount; ++i) {
        for (const auto &coin : coins) {
            if (i - coin >= 0) {
                dp[i] = min(dp[i], dp[i - coin] + 1);
            }
        }
    }
    return dp.back() > amount ? -1 : dp.back();
}
```

注意

> dp[i-a[j]] 决策 a[j]是否参与

### [backpack](https://www.lintcode.com/problem/backpack/description)

> 在 n 个物品中挑选若干物品装入背包，最多能装多满？假设背包的大小为 m，每个物品的大小为 A[i]

```c++
int backPack(int m, vector<int> &A) {
    // write your code here
    // dp[i][j] 前i个物品，是否能装j
    // dp[i - 1][j] == true? 则不需要第i个物品也能装满
    // dp[i - 1][j - A[i - 1]]? 腾出第i个物品的空间，剩下空间能否装满
    // dp[i][j] = dp[i - 1][j] or dp[i - 1][j - A[i - 1]]
    vector<vector<bool>> dp(A.size() + 1, vector<bool>(m + 1));
    dp[0][0] = true;
    for (int i = 1; i <= A.size(); ++i) {
        for (int j = 0; j <= m; ++j) {
            dp[i][j] = dp[i - 1][j];
            if (j - A[i - 1] >= 0 && dp[i - 1][j - A[i - 1]]) {
                dp[i][j] = true;
            }
        }
    }
    for (int i = m; i >= 0; ++i) {
        if (dp[A.size()][i]) {
            return i;
        }
    }
    return 0;
}
```

### [backpack-ii](https://www.lintcode.com/problem/backpack-ii/description)

> 有 `n` 个物品和一个大小为 `m` 的背包. 给定数组 `A` 表示每个物品的大小和数组 `V` 表示每个物品的价值.
> 问最多能装入背包的总价值是多大?

思路：f[i][j] 前 i 个物品，装入 j 背包 最大价值

```c++
int backPackII(int m, vector<int> &A, vector<int> &V) {
    // write your code here
    // dp[i][j] 前i个物品，装入j背包 最大价值
    vector<vector<int>> dp(A.size() + 1, vector<int>(V.size() + 1));
    for (int i = 1; i <= A.size(); ++i) {
        for (int j = 0; j <= m; ++j) {
            // 不选第i个物品，则dp[i][j] = dp[i - 1][j]
            // 选了第i个物品，则dp[i][j] = dp[i - 1][j - A[i - 1]]，倒扣i的大小
            dp[i][j] = dp[i - 1][j];
            if (j - A[i - 1] >= 0) {
                dp[i][j] = max(dp[i][j], dp[i - 1][j - A[i - 1]] + V[i - 1]);
            }
        }
    }
    return dp.back().back();
}
```

## 练习

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
