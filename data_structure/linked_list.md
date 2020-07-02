# 链表

## 基本技能

链表相关的核心点

- null/nil 异常处理
- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点

## 常见题型

### [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

> 给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

```c++
class Solution {
public:
     ListNode* deleteDuplicates(ListNode* head) {
        auto current = head;
        while (current) {
            while (current->next && current->next->val == current->val) {
                current->next = current->next->next;
            }
            current = current->next;
        }
        return head;
    }
};
```

### [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

> 给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中   没有重复出现的数字。

思路：链表头结点可能被删除，所以用 dummy node 辅助删除

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        
        if (!head) {
            return head;
        }

        auto dummy = ListNode(0);
        dummy.next = head;
        head = &dummy;

        int repeatVal;
        while (head->next && head->next->next) {
            if (head->next->val != head->next->next->val) {
                head = head->next;
            } else {
                repeatVal = head->next->val;
                while (head->next && head->next->val == repeatVal) {
                    head->next = head->next->next;
                }
            }
        }

        return dummy.next;
    }
};
```

注意点
• A->B->C 删除 B，A.next = C
• 删除用一个 Dummy Node 节点辅助（允许头节点可变）
• 访问 X.next 、X.value 一定要保证 X != nil

### [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)

> 反转一个单链表。

思路：用一个 prev 节点保存向前指针，temp 保存向后的临时指针

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode *prev = nullptr;
        ListNode *next = nullptr;
        while (head) {
            next = head->next;
            head->next = prev;
            prev = head;
            head = next;
        }
        return prev;
    }
};
```

### [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

> 反转从位置  *m*  到  *n*  的链表。请使用一趟扫描完成反转。

思路：先遍历到 m 处，翻转，再拼接后续，注意指针处理

```c++
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        if (!head) {
            return nullptr;
        }
        auto dummy = ListNode(0);
        dummy.next = head;
        head = &dummy;

        ListNode *prev = nullptr;
        auto index = 0;
        for (; index < m; ++index) {
            prev = head;
            head = head->next;
        }

        auto rend = head;
        ListNode *newNext = nullptr;
        for (; index <= n; ++index) {
            auto nextNode = head->next;
            head->next = newNext;
            newNext = head;
            head = nextNode;
        }
        prev->next = newNext;
        rend->next = head;

        return dummy.next;
    }
};
```

### [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

> 将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

思路：通过 dummy node 链表，连接各个元素

```c++
// 递归实现
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    if (!l1 || !l2) {
        return l1 ? l1 : l2;
    }
    ListNode *ret;
    if (l1->val < l2->val) {
        ret = l1;
        ret->next = mergeTwoLists(l1->next, l2);
    } else {
        ret = l2;
        ret->next = mergeTwoLists(l1, l2->next);
    }
    return ret;
}

// dummy node实现
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    if (!l1 || !l2) {
        return l1 ? l1 : l2;
    }
    auto dummy = ListNode(0);
    ListNode *iter = &dummy;
    while (l1 && l2) {
        if (l1->val < l2->val) {
            iter->next = l1;
            l1 = l1->next;
        } else {
            iter->next = l2;
            l2 = l2->next;
        }
        iter = iter->next;
    }
    iter->next = l1 ? l1 : l2;
    return dummy.next;
}
```

### [partition-list](https://leetcode-cn.com/problems/partition-list/)

> 给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于  *x*  的节点都在大于或等于  *x*  的节点之前。

思路：将大于 x 的节点，放到另外一个链表，最后连接这两个链表

```c++
class Solution {
public:
    // 思路：将大于x的节点，放到另外一个链表，最后连接这两个链表
    ListNode* partition(ListNode* head, int x) {
        if (!head) {
            return nullptr;
        }
        ListNode dummy{0};
        dummy.next = head;
        head = &dummy;
        ListNode greaterList{0};
        auto tail = &greaterList;
        while (head->next) {
            if (head->next->val < x) {
                head = head->next;
                continue;
            }
            tail->next = head->next;
            tail = tail->next;
            head->next = head->next->next;
        }
        tail->next = nullptr;
        head->next = greaterList.next;
        return dummy.next;
    }
};
```

哑巴节点使用场景

> 当头节点不确定的时候，使用哑巴节点

### [sort-list](https://leetcode-cn.com/problems/sort-list/)

> 在  *O*(*n* log *n*) 时间复杂度和常数级空间复杂度下，对链表进行排序。

思路：归并排序，找中点和合并操作

```c++
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        return mergeSort(head);
    }

    ListNode* mergeSort(ListNode *head) {
        if (!head || !head->next) {
            return head;
        }
        auto middle = findMiddle(head);
        auto left = head;
        auto right = middle->next;
        middle->next = nullptr;
        left = mergeSort(left);
        right = mergeSort(right);
        return mergeTwoLists(left, right);
    }

    ListNode* findMiddle(ListNode *node) {
        auto slow = node;
        auto fast = node->next;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }

    ListNode *mergeTwoLists(ListNode *l1, ListNode *l2) {
        if (!l1 || !l2) {
            return l1 ? l1 : l2;
        }
        auto dummy = ListNode(0);
        ListNode *iter = &dummy;
        while (l1 && l2) {
            if (l1->val < l2->val) {
                iter->next = l1;
                l1 = l1->next;
            } else {
                iter->next = l2;
                l2 = l2->next;
            }
            iter = iter->next;
        }
        // 这里直接接上即可，不用一个个拼接
        iter->next = l1 ? l1 : l2;
        return dummy.next;
    }
};
```

注意点

- 快慢指针 判断 fast 及 fast.Next 是否为 nil 值
- 递归 mergeSort 需要断开中间节点
- 递归返回条件为 head 为 nil 或者 head.Next 为 nil

### [reorder-list](https://leetcode-cn.com/problems/reorder-list/)

> 给定一个单链表  *L*：*L*→*L*→…→*L\_\_n*→*L*
> 将其重新排列后变为： *L*→*L\_\_n*→*L*→*L\_\_n*→*L*→*L\_\_n*→…

思路：找到中点断开，翻转后面部分，然后合并前后两个链表

```c++
class Solution {
public:
    /*
     * 给定一个单链表 L：L0→L1→…→Ln-1→Ln ，
     * 将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…
     */
    void reorderList(ListNode* head) {
        if (!head) {
            return;
        }
        auto middle = findMiddle(head);
        auto left = head;
        auto right = middle->next;
        middle->next = nullptr;
        right = revertList(right);
        while (left && right) {
            auto nextLeft = left->next;
            auto nextRight = right->next;
            left->next = right;
            right->next = nextLeft;
            left = nextLeft;
            right = nextRight;
        }
    }

    ListNode* revertList(ListNode *head) {
        if (!head) {
            return head;
        }
        auto prev = head;
        head = head->next;
        prev->next = nullptr;
        while (head) {
            auto next = head->next;
            head->next = prev;
            prev = head;
            head = next;
        }
        return prev;
    }

    ListNode* findMiddle(ListNode *node) {
        auto slow = node;
        auto fast = node->next;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }
};
```

### [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)

> 给定一个链表，判断链表中是否有环。

思路：快慢指针，快慢指针相同则有环，证明：如果有环每走一步快慢指针距离会减 1
![fast_slow_linked_list](https://img.fuiboom.com/img/fast_slow_linked_list.png)

```c++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if (!head) {
            return false;
        }
        auto slow = head;
        auto fast = head->next;
        while (fast && fast->next) {
            if (fast == slow) {
                return true;
            }
            slow = slow->next;
            fast = fast->next->next;
        }
        return false;
    }
};
```

### [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

> 给定一个链表，返回链表开始入环的第一个节点。  如果链表无环，则返回  `null`。

思路：快慢指针，快慢相遇之后，慢指针回到头，快慢指针步调一致一起移动，相遇点即为入环点
![cycled_linked_list](https://img.fuiboom.com/img/cycled_linked_list.png)

```c++
/*
 * 思路：
 * 设: 入环前的长度为x，相遇位置离入环位置为y，离"终点"距离为z,链表节点数为n，环的长度为c，相遇时已经绕环k次
 * n = x + y + z
 * 
 * 相遇时
 * slow = x + y
 * fast = 2(x + y) + 1 = n + y + kc
 *  => 2x + y + 1 = x + y + z + kc
 *  => x + 1 = z + kc
 *  => x = z - 1 + kc
 * 所以除了从头走x外，还需要另一边先往前走一格
 */
ListNode *detectCycle(ListNode *head) {
    if (!head) {
        return nullptr;
    }
    auto slow = head;
    auto fast = head->next;
    while (fast && fast->next) {
        if (fast == slow) {
            fast = head;
            slow = slow->next;
            while (fast != slow) {
                slow = slow->next;
                fast = fast->next;
            }
            return slow;
        }
        fast = fast->next->next;
        slow = slow->next;
    }
    return nullptr;
}
```

坑点

- 指针比较时直接比较对象，不要用值比较，链表中有可能存在重复值情况
- 第一次相交后，快指针需要从下一个节点开始和头指针一起匀速移动

另外一种方式是 fast=head,slow=head

```c++
/*
 * 思路：
 * 设: 入环前的长度为x，相遇位置离入环位置为y，离"终点"距离为z,链表节点数为n，环的长度为c，相遇时已经绕环k次
 * n = x + y + z
 * 
 * 第一次相遇时
 * slow = x + y
 * fast = 2(x + y) = n + y + kc
 *  => 2x + y = x + y + z + kc
 *  => x = z + kc
 * 又，相遇位置离终点（也是入环位置）为z，z + kc还是入环位置；
 * 起点到入环位置也为x
 * 
 * 所以，把slow丢回起点，fast速度降为1继续走，必定在slow走到入环位置时与fast相遇
 */
ListNode *detectCycle(ListNode *head) {
    if (!head) {
        return nullptr;
    }
    auto slow = head;
    auto fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (fast == slow) {
            slow = head;
            while (fast != slow) {
                slow = slow->next;
                fast = fast->next;
            }
            return slow;
        }
    }
    return nullptr;
}
```

这两种方式不同点在于，**一般用 fast=head.Next 较多**，因为这样可以知道中点的上一个节点，可以用来删除等操作。

- fast 如果初始化为 head.Next 则中点在 slow.Next
- fast 初始化为 head,则中点在 slow

### [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)

> 请判断一个链表是否为回文链表。

```c++
bool isPalindrome(ListNode* head) {
    if (!head) {
        return true;
    }

    /*
     * fast 如果初始化为 head.Next 则中点在 slow.Next
     * fast 初始化为 head,则中点在 slow
     */
    auto slow = head;
    auto fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    auto half = reverse(slow);
    slow->next = nullptr;
    while (head && half) {
        if (head->val != half->val) {
            return false;
        }
        head = head->next;
        half = half->next;
    }
    return true;
}

ListNode* reverse(ListNode *head) {
    if (!head) {
        return head;
    }
    ListNode *prev = nullptr;
    while (head) {
        auto next = head->next;
        head->next = prev;
        prev = head;
        head = next;
    }
    return prev;
}
```

### [copy-list-with-random-pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

> 给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。
> 要求返回这个链表的 深拷贝。

思路：1、hash 表存储指针，2、复制节点跟在原节点后面

```c++
Node* copyRandomList(Node* head) {
    if (!head) {
        return head;
    }

    // clone node
    auto iter = head;
    while (iter) {
        auto cloned = new Node(iter->val);
        cloned->next = iter->next;
        cloned->random = iter->random;
        iter->next = cloned;
        iter = cloned->next;
    }

    // update random
    iter = head;
    while (iter) {
        if (iter->random) {
            iter->next->random = iter->random->next;
        }
        iter = iter->next->next;
    }

    // split two list and make sure the original version isn't changed
    iter = head;
    auto cloneHead = head->next;
    while (iter && iter->next) {
        auto tmp = iter->next;
        iter->next = iter->next->next;
        iter = tmp;
    }
    return cloneHead;
}
```

## 总结

链表必须要掌握的一些点，通过下面练习题，基本大部分的链表类的题目都是手到擒来~

- null/nil 异常处理
- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点

## 练习

- [ ] [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)
- [ ] [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)
- [ ] [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)
- [ ] [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)
- [ ] [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)
- [ ] [partition-list](https://leetcode-cn.com/problems/partition-list/)
- [ ] [sort-list](https://leetcode-cn.com/problems/sort-list/)
- [ ] [reorder-list](https://leetcode-cn.com/problems/reorder-list/)
- [ ] [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)
- [ ] [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
- [ ] [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)
- [ ] [copy-list-with-random-pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)
