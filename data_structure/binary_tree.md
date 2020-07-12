# 二叉树

## 知识点

### 二叉树遍历

**前序遍历**：**先访问根节点**，再前序遍历左子树，再前序遍历右子树
**中序遍历**：先中序遍历左子树，**再访问根节点**，再中序遍历右子树
**后序遍历**：先后序遍历左子树，再后序遍历右子树，**再访问根节点**

注意点

- 以根访问顺序决定是什么遍历
- 左子树都是优先右子树

#### 前序递归

```c++
void preorderTraversal(TreeNode *root)  {
    if (!root) {
        return;
    }
    cout << root->val << " ";
    preOrderTraversal(root->left);
    preOrderTraversal(root->right);
}
```

#### 前序非递归

```c++
// V3：通过非递归遍历
void preorderTraversal(TreeNode *root) {
    if (!root) {
        return;
    }
    auto stack = vector<TreeNode *>();
    while (root || !stack.empty()) {
        if (!root) {
            root = stack.back();
            stack.pop_back();
        }
        cout << root->val << " ";
        if (root->right) {
            stack.push_back(root->right);
        }
        root = root->left;
    }
}
```

#### 中序非递归

```c++
// 思路：通过stack 保存已经访问的元素，用于原路返回
void inorderTraversal(TreeNode *root) {
    if (!root) {
        return;
    }
    auto stack = vector<TreeNode *>();

    while (root || !stack.empty()) {
        while (root) {
            stack.push_back(root);
            root = root->left;
        }
        root = stack.back();
        stack.pop_back();
        cout << root->val << " ";
        root = root->right;
    }
}
```

#### 后序非递归

```c++
void postorderTraversal(TreeNode *root) {
    if (!root) {
        return;
    }
    auto stack = vector<TreeNode *>();
    TreeNode *lastPop = nullptr;
    while (root || !stack.empty()) {
        // 每次找到最左下节点
        while (root) {
            stack.push_back(root);
            root = root->left;
        }
        root = stack.back();
        // 后序遍历，不能直接弹出，先检查是否有右子节点
        // 右节点要注意有可能已经遍历过了，又撤回来到当前节点
        // 只要保存上次弹出的节点即可！上次弹出的必定是其右子节点
        if (root->right && root->right != lastPop) {
            root = root->right;
            continue;
        }
        cout << root->val << " ";
        stack.pop_back();
        lastPop = root;
        root = nullptr;
    }
}
```

注意点

- 核心就是：根节点必须在右节点弹出之后，再弹出

#### DFS 深度搜索-从上到下

```c++
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
};

vector<int> preOrderTraversal(TreeNode *root) {
    vector<int> result;
    dfs(root, result);
    return result;
}

void dfs(TreeNode *root, vector<int> &result) {
    if (!root) {
        return;
    }
    result.push_back(root->val);
    dfs(root->left, result);
    dfs(root->right, result);
}
```

#### DFS 深度搜索-从下向上（分治法）

```c++
// V2：通过分治法遍历
vector<int> preOrderTraversal(TreeNode *root) {
    return divideAndConquer(root);
}

vector<int> divideAndConquer(TreeNode *root) {
    vector<int> result;
    if (!root) {
        return result;
    }
    // 分治(Divide)
    auto left = divideAndConquer(root->left);
    auto right = divideAndConquer(root->right);
    // 合并结果(Conquer)
    result.push_back(root->val);
    result.insert(result.end(), left.begin(), left.end());
    result.insert(result.end(), right.begin(), right.end());
    return result;
}
```

注意点：

> DFS 深度搜索（从上到下） 和分治法区别：前者一般将最终结果通过指针参数传入，后者一般递归返回结果最后合并

#### BFS 层次遍历

```c++
vector<int> levelOrder(TreeNode *root) {
    vector<int> result;
    if (!root) {
        return result;
    }
    queue<TreeNode *> queue;
    queue.push(root);
    while (!queue.empty()) {
        root = queue.front();
        result.push_back(root->val);
        if (root->left) {
            queue.push(root->left);
        }
        if (root->right) {
            queue.push(root->right);
        }
        queue.pop();
    }

    return result;
}
```

### 分治法应用

先分别处理局部，再合并结果

适用场景

- 快速排序
- 归并排序
- 二叉树相关问题

分治法模板

- 递归返回条件
- 分段处理
- 合并结果

```c++
ResultType traversal(TreeNode *root) {
    // nil or leaf
    if (!root) {
        // do something and return
    }

    // Divide
    auto left = traversal(root->left);
    auto right = traversal(root->right);

    // Conquer
    auto result = merge(left, right);

    return result;
}
```

#### 典型示例

```c++
// V2：通过分治法遍历二叉树
vector<int> preOrderTraversal(TreeNode *root) {
    return divideAndConquer(root);
}

vector<int> divideAndConquer(TreeNode *root) {
    vector<int> result;
    if (!root) {
        return result;
    }
    // 分治(Divide)
    auto left = divideAndConquer(root->left);
    auto right = divideAndConquer(root->right);
    // 合并结果(Conquer)
    result.push_back(root->val);
    result.insert(result.end(), left.begin(), left.end());
    result.insert(result.end(), right.begin(), right.end());
    return result;
}
```

#### 归并排序  

```c++
template<typename T>
static void MergeSort(T arr[], int len) {
    auto tmp = new T[len];
    mergeSort(arr, 0, len - 1, tmp);
    delete[] tmp;
}

template<typename T>
static void mergeSort(T arr[], int begin, int end, T tmp[]) {
    if (begin + 1 >= end) {
        return;
    }

    auto mid = begin + (end - begin) / 2;
    auto begin1 = begin;
    auto end1 = mid;
    auto begin2 = mid + 1;
    auto end2 = end;
    mergeSort(arr, begin1, end1, tmp);
    mergeSort(arr, begin2, end2, tmp);

    // merge two parts
    auto index = begin;
    while (begin1 <= end1 && begin2 <= end2) {
        tmp[index++] = arr[begin1] < arr[begin2] ? arr[begin1++] : arr[begin2++];
    }

    while (begin1 <= end1) {
        tmp[index++] = arr[begin1++];
    }

    while (begin2 <= end2) {
        tmp[index++] = arr[begin2++];
    }

    for (int i = begin; i <= end; ++i) {
        arr[i] = tmp[i];
    }
}
```

注意点

> 递归需要返回结果用于合并

#### 快速排序  

```c++
template<typename T>
static void QuickSort(T arr[], int len) {
    quickSort(arr, 0, len - 1);
}

template<typename T>
static void quickSort(T arr[], int begin, int end) {
    if (begin >= end) {
        return;
    }
    auto pivot = partition(arr, begin, end);
    quickSort(arr, begin, pivot - 1);
    quickSort(arr, pivot + 1, end);
}

template<typename T>
static int partition(T arr[], int begin, int end) {
    auto base = arr[end];
    auto lessInsert = begin;
    for (int i = begin; i < end; ++i) {
        if (arr[i] < base) {
            swap(arr[lessInsert++], arr[i]);
        }
    }
    swap(arr[lessInsert], arr[end]);
    return lessInsert;
}
```

注意点：

> 快排由于是原地交换所以没有合并过程
> 传入的索引是存在的索引（如：0、length-1 等），越界可能导致崩溃

常见题目示例

#### maximum-depth-of-binary-tree

[maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

> 给定一个二叉树，找出其最大深度。

思路：分治法

```c++
int maxDepth(TreeNode* root) {
    if (!root) {
        return 0;
    }
    // divide：分左右子树分别计算
    auto leftDepth = maxDepth(root->left);
    auto rightDepth = maxDepth(root->right);
    
    // conquer：合并左右子树结果
    return (leftDepth > rightDepth ? leftDepth : rightDepth) + 1;
}
```

#### balanced-binary-tree

[balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

> 给定一个二叉树，判断它是否是高度平衡的二叉树。

思路：分治法，左边平衡 && 右边平衡 && 左右两边高度 <= 1，
因为需要返回是否平衡及高度，要么返回两个数据，要么合并两个数据，
所以用-1 表示不平衡，>0 表示树高度（二义性：一个变量有两种含义）。

```c++
bool isBalanced(TreeNode *root) {
    return maxDepth(root) != -1;
}

int maxDepth(TreeNode *root) {
    if (!root) {
        return 0;
    }
    auto left = maxDepth(root->left);
    auto right = maxDepth(root->right);
    if (left == -1 || right == -1 || left - right > 1 || right - left > 1) {
        return -1;
    }
    return (left > right ? left : right) + 1;
}
```

注意

> 一般工程中，结果通过两个变量来返回，不建议用一个变量表示两种含义

#### binary-tree-maximum-path-sum

[binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

> 给定一个**非空**二叉树，返回其最大路径和。

思路：分治法，分为三种情况：左子树最大路径和最大，右子树最大路径和最大，左右子树最大加根节点最大，需要保存两个变量：一个保存子树最大路径和，一个保存左右加根节点和，然后比较这个两个变量选择最大值即可

```c++
int maxPathSum(TreeNode *root) {
    auto maxSum = std::numeric_limits<int>::min();
    calcMaxPath(root, maxSum);
    return maxSum;
}

int calcMaxPath(TreeNode *&root, int &maxSum) {
    if (!root) {
        return 0;
    }
    // 如果左右子树为负，直接丢弃该部分（不需要从一个叶子节点走到另一个叶子）设置为0
    auto left = max(calcMaxPath(root->left), 0);
    auto right = max(calcMaxPath(root->right), 0);

    // 取当前值与当前"闭环"（左右子树+当前节点）的最大值
    // 注意，初始值要设置为最小数！
    // 子树可以不舍弃负数，但是结果不能（必须至少经过一个节点）
    // 当前节点为叶子时，right + left + root->val = 0 + 0 + root->val
    maxSum = max(maxSum, left + right + root->val);
    // 已经处理过同时涉及左右子树+当前节点的可能
    // 对于后续处理，只有一种可能，以当前节点为根的这颗树，取一条路径返回——左右子树最大值 + 当前节点
    return max(left, right) + root->val;
}
```

#### lowest-common-ancestor-of-a-binary-tree

[lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

思路：分治法，有左子树的公共祖先或者有右子树的公共祖先，就返回子树的祖先，否则返回根节点

```c++
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (!root) {
        return nullptr;
    }
    // 相等 直接返回root节点即可
    if (root == p || root == q) {
        return root;
    }
    // Divide
    auto left = lowestCommonAncestor(root->left, p, q);
    auto right = lowestCommonAncestor(root->right, p, q);
    // Conquer
    // 左右两边都不为空，则根节点为祖先
    if (left && right) {
        return root;
    }
    return left ? left : right;
}
```

### BFS 层次应用

#### binary-tree-level-order-traversal

[binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

> 给你一个二叉树，请你返回其按  **层序遍历**  得到的节点值。 （即逐层地，从左到右访问所有节点）

思路：用一个队列记录一层的元素，然后扫描这一层元素添加下一层元素到队列（一个数进去出来一次，所以复杂度 O(logN)）

```c++
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> ret;
    if (!root) {
        return ret;
    }
    queue<TreeNode*> queue;
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

#### binary-tree-level-order-traversal-ii

[binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

> 给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

思路：在层级遍历的基础上，翻转一下结果即可

```c++
vector<vector<int>> levelOrderBottom(TreeNode *root) {
    auto result = levelOrder(root);
    reverse(result);
    return result;
}

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

void reverse(vector<vector<int>> &result) {
    for (int i = 0, j = result.size() - 1; i < j; ++i, --j) {
        swap(result[i], result[j]);
    }
}
```

#### binary-tree-zigzag-level-order-traversal

[binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

> 给定一个二叉树，返回其节点值的锯齿形层次遍历。Z 字形遍历

```c++
vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
    vector<vector<int>> ret;
    if (!root) {
        return ret;
    }
    queue<TreeNode *> queue;
    queue.push(root);
    auto l2r = true;
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
        if (l2r) {
            ret.emplace_back(levelValues);
        } else {
            ret.emplace_back(
                    levelValues.rbegin(),
                    levelValues.rend()
            );
        }
        l2r = !l2r;
    }
    return ret;
}
```

### 二叉搜索树应用

#### validate-binary-search-tree

[validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。

思路 1：中序遍历，检查结果列表是否已经有序

思路 2：分治法，判断左 MAX < 根 < 右 MIN

```c++
// v1
bool isValidBST(TreeNode *root) {
    if (!root) {
        return true;
    }
    // 思路 1：中序遍历，检查结果列表是否已经有序
    auto inOrderValues = vector<int>();
    inOrder(root, inOrderValues);
    for (auto iter = inOrderValues.cbegin() + 1; iter != inOrderValues.cend(); ++iter) {
        if (*(iter - 1) >= *iter) {
            return false;
        }
    }
    return true;
}

void inOrder(TreeNode *root, vector<int> &values) {
    if (!root) {
        return;
    }
    inOrder(root->left, values);
    values.push_back(root->val);
    inOrder(root->right, values);
}
```

```c++
// v2分治法
struct Result {
    TreeNode *maxNode;
    TreeNode *minNode;
    bool isValidate;

    Result(bool validate = true, TreeNode *max = nullptr, TreeNode *min = nullptr)
            : isValidate(validate), maxNode(max), minNode(min) {

    }
};
bool isValidBST(TreeNode *root) {
    if (!root) {
        return true;
    }
    return helper(root).isValidate;
}

Result helper(TreeNode *root) {
    if (!root) {
        return {};
    }
    auto left = helper(root->left);
    auto right = helper(root->right);
    if (!(left.isValidate && right.isValidate)) {
        return {false};
    }
    if (left.maxNode && left.maxNode->val >= root->val) {
        return {false};
    }
    if (right.minNode && right.minNode->val <= root->val) {
        return {false};
    }
    return {
            true,
            right.maxNode ? right.maxNode : root,
            left.minNode ? left.minNode : root,
    };
}
```

#### insert-into-a-binary-search-tree

[insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。

思路：找到最后一个叶子节点满足插入条件即可

```c++
// DFS查找插入位置
TreeNode* insertIntoBST(TreeNode* root, int val) {
    if (!root) {
        root = new TreeNode(val);
        return root;
    }

    if (root->val > val) {
        root->left = insertIntoBST(root->left, val);
    } else {
        root->right = insertIntoBST(root->right, val);
    }
    return root;
}
```

## 总结

- 掌握二叉树递归与非递归遍历
- 理解 DFS 前序遍历与分治法
- 理解 BFS 层次遍历

## 练习

- [ ] [maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)
- [ ] [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)
- [ ] [binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
- [ ] [lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [ ] [binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
- [ ] [binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)
- [ ] [binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
- [ ] [validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)
- [ ] [insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
