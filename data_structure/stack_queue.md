# 栈和队列

## 简介

栈的特点是后入先出

![image.png](https://img.fuiboom.com/img/stack.png)

根据这个特点可以临时保存一些数据，之后用到依次再弹出来，常用于 DFS 深度搜索

队列一般常用于 BFS 广度搜索，类似一层一层的搜索

## Stack 栈

[min-stack](https://leetcode-cn.com/problems/min-stack/)

> 设计一个支持 push，pop，top 操作，并能在常数时间内检索到最小元素的栈。

思路：用两个栈实现，一个最小栈始终保证最小值在顶部

```c++
class MinStack {
private:
    vector<int> valueStack;
    vector<int> minStack;

public:
    /** initialize your data structure here. */
    MinStack() {

    }

    void push(int x) {
        valueStack.push_back(x);
        auto curMin = getMin();
        if (curMin < x) {
            minStack.push_back(curMin);
        } else {
            minStack.push_back(x);
        }
    }

    void pop() {
        valueStack.pop_back();
        minStack.pop_back();
    }

    int top() {
        if (valueStack.empty()) {
            return 0;
        }
        return valueStack.back();
    }

    int getMin() {
        if (minStack.empty()) {
            return numeric_limits<int>::max();
        }
        return minStack.back();
    }
};
```

[evaluate-reverse-polish-notation](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

> **波兰表达式计算** > **输入:** ["2", "1", "+", "3", "*"] > **输出:** 9
> **解释:** ((2 + 1) \* 3) = 9

思路：通过栈保存原来的元素，遇到表达式弹出运算，再推入结果，重复这个过程

```c++
class Solution {
public:
    int evalRPN(vector<string> &tokens) {
       if (tokens.empty()) {
            return 0;
        }
        auto token = tokens.back();
        tokens.pop_back();
        if (token != "+" && token != "-" && token != "*" && token != "/") {
            return atoi(token);
        }
        auto rhs = evalRPN(tokens);
        auto lhs = evalRPN(tokens);
        if (token == "+") {
            return lhs + rhs;
        } else if (token == "-") {
            return lhs - rhs;
        } else if (token == "*") {
            return lhs * rhs;
        } else if (token == "/") {
            return lhs / rhs;
        }
        return -1;
    }

    int atoi(const string &str) {
        if (str[0] == '-') {
            return -atoi(str.substr(1));
        }
        int ret = 0;
        for (const auto &item : str) {
            ret = ret * 10 + item - '0';
        }
        return ret;
    }
};
```

[decode-string](https://leetcode-cn.com/problems/decode-string/)

> 给定一个经过编码的字符串，返回它解码后的字符串。
> s = "3[a]2[bc]", 返回 "aaabcbc".
> s = "3[a2[c]]", 返回 "accaccacc".
> s = "2[abc]3[cd]ef", 返回 "abcabccdcdcdef".

思路：通过栈辅助进行操作

```c++
class Solution {
public:
string decodeString(string s) {
        if (s.empty()) {
            return "";
        }

        vector<char> stack;
        for (const auto &c : s) {
            if (c != ']') {
                stack.push_back(c);
                continue;
            }
            vector<char> subStr;
            while (stack.back() != '[') {
                subStr.push_back(stack.back());
                stack.pop_back();
            }
            stack.pop_back();

            int digitBegin = stack.size() - 1;
            for (; digitBegin != 0; --digitBegin) {
                auto val = stack[digitBegin];
                if (!('0' <= val && val <= '9')) {
                    ++digitBegin;
                    break;
                }
            }
            auto repeat = atoi({
                                    stack.begin() + digitBegin,
                                    stack.end(),
                            });
            stack.resize(digitBegin);
            for (int i = 0; i < repeat; ++i) {
                stack.insert(stack.end(), subStr.rbegin(), subStr.rend());
            }
        }
        return {
            stack.begin(),
            stack.end()
        };
    }

    int atoi(const string &str) {
        if (str.empty()) {
            return 0;
        }
        int value = 0;
        for (const auto &c : str) {
            value = value * 10 + c - '0';
        }
        return value;
    }
};
```

利用栈进行 DFS 递归搜索模板

```c++
bool DFS(Node root, Node target) {
    set<Node> visited;
    stack<Node> stack;
    stack.push(root);
    while (!stack.empty()) {
        auto cur = stack.top();
        return true if cur is target;
        for (auto next : the neighbors of cur) {
            if (visited.find(target) == visited.end() {
                stack.push(next);
                visited.insert(target);
            }
        }
        stack.pop();
    }
    return false;
}
```

[binary-tree-inorder-traversal](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

> 给定一个二叉树，返回它的*中序*遍历。

```c++
// 思路：通过stack 保存已经访问的元素，用于原路返回
vector<int> inorderTraversal(TreeNode* root) {
    if (!root) {
        return {};
    }
    vector<int> ret;
    vector<TreeNode*> stack;
    while (root || !stack.empty()) {
        while (root) {
            // 从最左下节点开始
            stack.push_back(root);
            root = root->left;
        }
        root = stack.back();
        stack.pop_back();
        ret.push_back(root->val);
        root = root->right;
    }
    return ret;
}
```

[clone-graph](https://leetcode-cn.com/problems/clone-graph/)

> 给你无向连通图中一个节点的引用，请你返回该图的深拷贝（克隆）。

```c++
Node* cloneGraph(Node* node) {
    map<Node*, Node*> newNodes;
    return clone(node, newNodes);
}

Node* clone(Node *node, map<Node*, Node*> &genNodes) {
    if (!node) {
        return nullptr;
    }
    if (genNodes.find(node) != genNodes.end()) {
        return genNodes[node];
    }
    auto newNode = new Node(node->val);
    genNodes[node] = newNode;
    for (const auto &neighbor : node->neighbors) {
        newNode->neighbors.push_back(clone(neighbor, genNodes));
    }
    return newNode;
}
```

[number-of-islands](https://leetcode-cn.com/problems/number-of-islands/)

> 给定一个由  '1'（陆地）和 '0'（水）组成的的二维网格，计算岛屿的数量。一个岛被水包围，并且它是通过水平方向或垂直方向上相邻的陆地连接而成的。你可以假设网格的四个边均被水包围。

思路：通过深度搜索遍历可能性（注意标记已访问元素）

```c++
int numIslands(vector<vector<char>> &grid) {
    auto num = 0;
    for (int i = 0; i < grid.size(); ++i) {
        for (int j = 0; j < grid[i].size(); ++j) {
            if (grid[i][j] == '1') {
                flowTheIsland(grid, i, j);
                ++num;
            }
        }
    }
    return num;
}

void flowTheIsland(vector<vector<char>> &grid, int i, int j) {
    if (i < 0 || i >= grid.size() || j < 0 || j >= grid[i].size() || grid[i][j] == '0') {
        return;
    }
    grid[i][j] = '0';
    flowTheIsland(grid, i - 1, j);
    flowTheIsland(grid, i + 1, j);
    flowTheIsland(grid, i, j + 1);
    flowTheIsland(grid, i, j - 1);
}
```

[largest-rectangle-in-histogram](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

> 给定 _n_ 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。
> 求在该柱状图中，能够勾勒出来的矩形的最大面积。

思路：求以当前柱子为高度的面积，即转化为寻找小于当前值的左右两边值

![image.png](https://img.fuiboom.com/img/stack_rain.png)

用栈保存小于当前值的左的元素

![image.png](https://img.fuiboom.com/img/stack_rain2.png)

```c++
#pragma region 暴力解法, 时间复杂度O(n^2)不满足
int largestRectangleArea0(vector<int> &heights) {
    auto largest = 0;
    for (int i = 0; i < heights.size(); ++i) {
        auto area = calcArea(heights, i);
        if (area > largest) {
            largest = area;
        }
    }
    return largest;
}

int calcArea(const vector<int> &heights, int col) {
    auto height = heights[col];
    auto begin = col;
    while (begin > 0) {
        if (heights[begin - 1] >= height) {
            --begin;
        } else {
            break;
        }
    }
    auto end = col;
    while (end < heights.size() - 1) {
        if (heights[end + 1] >= height) {
            ++end;
        } else {
            break;
        }
    }
    return height * (end - begin + 1);
}
#pragma endregion 暴力解法

#pragma region 单调栈 + 哨兵
/* 若当前柱子的高度小于栈中元素，则弹出所有比它高的，并将当前柱子入栈
 * 因为会被最低的挡住
 * 结果就是栈中元素保持单调递增
 * 一次循环保存结果避免嵌套循环么
 *
 * 使用“哨兵”来为左右边界占位，避免特殊处理
 *
 * 注意事项：
 * 判断的时候要使用>=，弹出一样高度的柱子，以获得正确的边界！
 */
int largestRectangleArea1(vector<int> &heights) {
    auto size = heights.size();
    vector<int> left(size), right(size);

    stack<int> monoStack;
    for (int i = 0; i < size; ++i) {
        while (!monoStack.empty() && heights[monoStack.top()] >= heights[i]) {
            monoStack.pop();
        }
        left[i] = monoStack.empty() ? -1 : monoStack.top();
        monoStack.push(i);
    }

    monoStack = {};
    for (int i = size - 1; i >= 0; --i) {
        while (!monoStack.empty() && heights[monoStack.top()] >= heights[i]) {
            monoStack.pop();
        }
        right[i] = monoStack.empty() ? size : monoStack.top();
        monoStack.push(i);
    }

    auto largest = 0;
    for (int i = 0; i < size; ++i) {
        auto area = (right[i] - left[i] - 1) * heights[i];
        largest = largest > area ? largest : area;
    }
    return largest;
}
#pragma endregion

#pragma region 单调栈 + 常数优化
/*
 * 少一次循环
 *
 * 在从左往右遍历时
 * 入栈 = 左边界
 * 出栈 = “右边界”
 * 出栈，是>=，不是>，得到的右边界不准确？
 * 没关系，对于同高度的区域，最右边的柱子能够得到准确的高度！以它为准即可
 */
int largestRectangleArea(vector<int> &heights) {
    auto size = heights.size();
    vector<int> left(size), right(size, size);

    stack<int> monoStack;
    for (int i = 0; i < size; ++i) {
        while (!monoStack.empty() && heights[monoStack.top()] >= heights[i]) {
            right[monoStack.top()] = i;
            monoStack.pop();
        }
        left[i] = monoStack.empty() ? -1 : monoStack.top();
        monoStack.push(i);
    }

    auto largest = 0;
    for (int i = 0; i < size; ++i) {
        auto area = (right[i] - left[i] - 1) * heights[i];
        largest = largest > area ? largest : area;
    }
    return largest;
}
#pragma endregion
```

## Queue 队列

常用于 BFS 宽度优先搜索

[implement-queue-using-stacks](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

> 使用栈实现队列

```c++
class MyQueue {
private:
    vector<int> stack1;
    vector<int> stack2;
public:
    /** Initialize your data structure here. */
    MyQueue() {

    }

    /** Push element x to the back of queue. */
    void push(int x) {
        stack1.push_back(x);
    }

    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        auto ret = peek();
        stack2.pop_back();
        return ret;
    }

    /** Get the front element. */
    int peek() {
        if (!stack2.empty()) {
            return stack2.back();
        }
        stack2.insert(stack2.end(), stack1.rbegin(), stack1.rend());
        stack1.clear();
        return peek();
    }

    /** Returns whether the queue is empty. */
    bool empty() {
        return stack1.empty() && stack2.empty();
    }
};
```

二叉树层次遍历

```c++
vector<vector<int>> levelOrder(TreeNode *root) {
    vector<vector<int>> ret;
    if (!root) {
        return ret;
    }
    queue<TreeNode *> queue;
    queue.push(root);
    while (!queue.empty()) {
        vector<int> levelValues;
        for (auto levelNodeNum = queue.size(); levelNodeNum > 0; --levelNodeNum) {
            auto node = queue.front();
            queue.pop();
            levelValues.push_back(node->val);
            if (node->left) {
                queue.push(node->left);
            }
            if (node->right) {
                queue.push(node->right);
            }
        }
        ret.push_back(levelValues);
    }
    return ret;
}
```

[01-matrix](https://leetcode-cn.com/problems/01-matrix/)

> 给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。
> 两个相邻元素间的距离为 1

```c++
/*
 * 从0出发，而不是从1出发！
 * 把所有为0的节点压入队列，并进行广度优先遍历
 * 每次向外扩散一格
 */
vector<vector<int>> updateMatrix(vector<vector<int>>& matrix) {
    int row = matrix.size(), column = matrix[0].size();
    vector<vector<int>> dist(row, vector<int>(column));
    vector<vector<bool>> seen(row, vector<bool>(column));

    queue<pair<int, int>> q;
    for (int i = 0; i < row; ++i) {
        for (int j = 0; j < column; ++j) {
            if (matrix[i][j] == 0) {
                q.emplace(i, j);
                seen[i][j] = 1;
            }
        }
    }

    int dirs[4][2] = {{-1, 0},
                      {1,  0},
                      {0,  -1},
                      {0,  1}};
    while (!q.empty()) {
        auto[i, j] = q.front();
        q.pop();
        for (auto &dir : dirs) {
            int rowIdx = dir[0] + i;
            int colIdx = dir[1] + j;
            if (0 <= rowIdx && rowIdx < row 
                && 0 <= colIdx && colIdx < column
                && !seen[rowIdx][colIdx]) {
                dist[rowIdx][colIdx] = dist[i][j] + 1;
                q.emplace(rowIdx, colIdx);
                seen[rowIdx][colIdx] = true;
            }
        }
    }
    return dist;
}
```

## 总结

- 熟悉栈的使用场景
  - 后入先出，保存临时值
  - 利用栈 DFS 深度搜索
- 熟悉队列的使用场景
  - 利用队列 BFS 广度搜索

## 练习

- [ ] [min-stack](https://leetcode-cn.com/problems/min-stack/)
- [ ] [evaluate-reverse-polish-notation](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)
- [ ] [decode-string](https://leetcode-cn.com/problems/decode-string/)
- [ ] [binary-tree-inorder-traversal](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)
- [ ] [clone-graph](https://leetcode-cn.com/problems/clone-graph/)
- [ ] [number-of-islands](https://leetcode-cn.com/problems/number-of-islands/)
- [ ] [largest-rectangle-in-histogram](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)
- [ ] [implement-queue-using-stacks](https://leetcode-cn.com/problems/implement-queue-using-stacks/)
- [ ] [01-matrix](https://leetcode-cn.com/problems/01-matrix/)
