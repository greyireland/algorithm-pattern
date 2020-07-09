# 递归

## 介绍

将大问题转化为小问题，通过递归依次解决各个小问题

## 示例

[reverse-string](https://leetcode-cn.com/problems/reverse-string/)

> 编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组  `char[]`  的形式给出。

```c++
void reverseString(vector<char> &s) {
    reverse(s, 0, s.size() - 1);
}

void reverse(vector<char> &s, int left, int right) {
    if (left >= right) {
        return;
    }
    swap(s[left++], s[right--]);
    reverse(s, left, right);
}
```

[swap-nodes-in-pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

> 给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。
> **你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

```c++
ListNode* swapPairs(ListNode* head) {
    return swapper(head);
}

ListNode* swapper(ListNode *head) {
    if (head == nullptr || head->next == nullptr) {
        return head;
    }
    auto nextHead = head->next->next;
    auto newHead = head->next;
    newHead->next = head;
    head->next = swapper(nextHead);
    return newHead;
}
```

[unique-binary-search-trees-ii](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

> 给定一个整数 n，生成所有由 1 ... n 为节点所组成的二叉搜索树。

```c++
vector<TreeNode *> generateTrees(int n) {
    if (n == 0) {
        return {};
    }
    return generate(1, n);
}

vector<TreeNode *> generate(int start, int end) {
    if (start > end) {
        // 记得返回nullptr，不可返回空vector
        return {nullptr};
    }
    vector<TreeNode *> ans;
    for (int i = start; i <= end; ++i) {
        // 取i作为根节点，生成所有左右子树
        auto lefts = generate(start, i - 1);
        auto rights = generate(i + 1, end);
        // 拼接
        for (auto &left : lefts) {
            for (auto &right : rights) {
                auto root = new TreeNode(i);
                root->left = left;
                root->right = right;
                ans.push_back(root);
            }
        }
    }
    return ans;
}
```

## 递归+备忘录

[fibonacci-number](https://leetcode-cn.com/problems/fibonacci-number/)

> 斐波那契数，通常用  F(n) 表示，形成的序列称为斐波那契数列。该数列由  0 和 1 开始，后面的每一项数字都是前面两项数字的和。也就是：
> F(0) = 0,   F(1) = 1
> F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
> 给定  N，计算  F(N)。

```c++
unordered_map<int, int> memo;

int fib(int N) {
    return fibWithMemo(N);
}

int fibWithMemo(int N) {
    if (N < 2) {
        return N;
    }
    if (memo[N] != 0) {
        return memo[N];
    }
    auto ret = fibWithMemo(N - 1) + fib(N - 2);
    memo[N] = ret;
    return ret;
}
```

## 练习

- [ ] [reverse-string](https://leetcode-cn.com/problems/reverse-string/)
- [ ] [swap-nodes-in-pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)
- [ ] [unique-binary-search-trees-ii](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)
- [ ] [fibonacci-number](https://leetcode-cn.com/problems/fibonacci-number/)
