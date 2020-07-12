# 二叉搜索树

## 定义

- 每个节点中的值必须大于（或等于）存储在其左侧子树中的任何值。
- 每个节点中的值必须小于（或等于）存储在其右子树中的任何值。

## 应用

[validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> 验证二叉搜索树

```c++
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

[insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 保证原始二叉搜索树中不存在新值。

```c++
TreeNode *insertIntoBST(TreeNode *root, int val) {
    if (root == nullptr) {
        return new TreeNode(val);
    }
    if (root->val > val) {
        root->left = insertIntoBST(root->left, val);
    } else {
        root->right = insertIntoBST(root->right, val);
    }
    return root;
}
```

[delete-node-in-a-bst](https://leetcode-cn.com/problems/delete-node-in-a-bst/)

> 给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的  key  对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。

```c++
// 注意二叉搜索树的概念！
// 如果当前节点是其父节点的左子节点，则当前节点底下任何一个节点都要比该父节点小
// 反之亦然
TreeNode* deleteNode(TreeNode* root, int key) {
    if (root == nullptr) {
        return root;
    }
    if (root->val < key) {
        root->right = deleteNode(root->right, key);
        return root;
    }
    if (root->val > key) {
        root->left = deleteNode(root->left, key);
        return root;
    }
    // 每个节点中的值必须大于（或等于）存储在其左侧子树中的任何值。
    // 所以压根不需要考虑啥当前节点的父节点，必定当前节点的左右子树！
    if (root->left == nullptr) {
        return root->right;
    }
    if (root->right == nullptr) {
        return root->left;
    }
    auto iter = root->right;
    while (iter->left != nullptr) {
        iter = iter->left;
    }
    iter->left = root->left;
    return root->right;
}
```

[balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

> 给定一个二叉树，判断它是否是高度平衡的二叉树。

```c++
struct ResultType {
    int height;
    bool valid;
};

bool isBalanced(TreeNode *root) {
    return dfs(root).valid;
}

type ResultType struct{
    height int
    valid bool
}
func isBalanced(root *TreeNode) bool {
    return dfs(root).valid
}
func dfs(root *TreeNode)(result ResultType){
    if root==nil{
        result.valid=true
        result.height=0
        return
    }
    left:=dfs(root.Left)
    right:=dfs(root.Right)
    // 满足所有特点：二叉搜索树&&平衡
    if left.valid&&right.valid&&abs(left.height,right.height)<=1{
        result.valid=true
    }
    result.height=Max(left.height,right.height)+1
    return
}
func abs(a,b int)int{
    if a>b{
        return a-b
    }
    return b-a
}
func Max(a,b int)int{
    if a>b{
        return a
    }
    return b
}

```

## 练习

- [ ] [validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)
- [ ] [insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
- [ ] [delete-node-in-a-bst](https://leetcode-cn.com/problems/delete-node-in-a-bst/)
- [ ] [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)
