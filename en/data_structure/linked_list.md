# Linked List

## Basic Skills

Core points related to linked lists

- null/nil exception handling
- dummy node
- fast and slow pointers
- inserting a node into a sorted linked list
- removing a node from a linked list
- reversing a linked list
- merging two linked lists
- finding the middle node of a linked list

## Common Problem Types

### [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

> Given a sorted linked list, delete all duplicate elements so that each element appears only once.

```go
func deleteDuplicates(head *ListNode) *ListNode {
    current := head
    for current != nil {
        // Delete all duplicates before moving to the next element
        for current.Next != nil && current.Val == current.Next.Val {
            current.Next = current.Next.Next
        }
        current = current.Next
    }
    return head
}
```

### [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

> Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only the numbers from the original list that do not appear more than once.

Idea: the head node of the list may be deleted, so use a dummy node to help with deletion.

```go
func deleteDuplicates(head *ListNode) *ListNode {
    if head == nil {
        return head
    }
    dummy := &ListNode{Val: 0}
    dummy.Next = head
    head = dummy

    var rmVal int
    for head.Next != nil && head.Next.Next != nil {
        if head.Next.Val == head.Next.Next.Val {
            // Record the deleted value, used to check subsequent nodes
            rmVal = head.Next.Val
            for head.Next != nil && head.Next.Val == rmVal  {
                head.Next = head.Next.Next
            }
        } else {
            head = head.Next
        }
    }
    return dummy.Next
}
```

Points to note
• A->B->C, delete B, then A.next = C
• Use a Dummy Node to help with deletion (so the head node can change)
• To access X.next or X.value, always make sure X != nil

### [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)

> Reverse a singly linked list.

Idea: use a prev node to save the forward pointer, and temp to save the temporary backward pointer.

```go
func reverseList(head *ListNode) *ListNode {
    var prev *ListNode
    for head != nil {
        // Save the current head.Next node to prevent it from being overwritten after reassignment
        // State after one round: nil<-1 2->3->4
        //                       prev   head
        temp := head.Next
        head.Next = prev
        // move pre
        prev = head
        // move head
        head = temp
    }
    return prev
}
```

### [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

> Reverse the linked list from position *m* to *n*. Do it in one pass.

Idea: first traverse to m, reverse, then reconnect the rest, paying attention to pointer handling.

```go
func reverseBetween(head *ListNode, m int, n int) *ListNode {
    // Idea: first traverse to m, reverse, then reconnect the rest, paying attention to pointer handling
    // Input: 1->2->3->4->5->NULL, m = 2, n = 4
    if head == nil {
        return head
    }
    // The head changes, so use a dummy node
    dummy := &ListNode{Val: 0}
    dummy.Next = head
    head = dummy
    // At the beginning: 0->1->2->3->4->5->nil
    var pre *ListNode
    var i = 0
    for i < m {
        pre = head
        head = head.Next
        i++
    }
    // After traversing: 1(pre)->2(head)->3->4->5->NULL
    // i = 1
    var j = i
    var next *ListNode
    // Used to connect the middle node
    var mid = head
    for head != nil && j <= n {
        // First iteration: 1 nil<-2 3->4->5->nil
        temp := head.Next
        head.Next = next
        next = head
        head = temp
        j++
    }
    // The loop runs four times
    // After the loop ends: 1 nil<-2<-3<-4 5(head)->nil
    pre.Next = next
    mid.Next = head
    return dummy.Next
}
```

### [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

> Merge two ascending linked lists into a new ascending linked list and return it. The new list is formed by splicing together all the nodes of the two given lists.

Idea: use a dummy node list to connect each element.

```go
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode {
    dummy := &ListNode{Val: 0}
    head := dummy
    for l1 != nil && l2 != nil {
        if l1.Val < l2.Val {
            head.Next = l1
            l1 = l1.Next
        } else {
            head.Next = l2
            l2 = l2.Next
        }
        head = head.Next
    }
    // Connect the remaining unprocessed nodes of l1
    for l1 != nil {
        head.Next = l1
        head = head.Next
        l1 = l1.Next
    }
    // Connect the remaining unprocessed nodes of l2
    for l2 != nil {
        head.Next = l2
        head = head.Next
        l2 = l2.Next
    }
    return dummy.Next
}
```

### [partition-list](https://leetcode-cn.com/problems/partition-list/)

> Given a linked list and a specific value x, partition the list so that all nodes less than *x* come before the nodes greater than or equal to *x*.

Idea: move nodes greater than x to another linked list, then connect the two lists at the end.

```go
func partition(head *ListNode, x int) *ListNode {
    // Idea: move nodes greater than x to another linked list, then connect the two lists at the end
    // check
    if head == nil {
        return head
    }
    headDummy := &ListNode{Val: 0}
    tailDummy := &ListNode{Val: 0}
    tail := tailDummy
    headDummy.Next = head
    head = headDummy
    for head.Next != nil {
        if head.Next.Val < x {
            head = head.Next
        } else {
            // Remove the node >= x
            t := head.Next
            head.Next = head.Next.Next
            // Put it into the other linked list
            tail.Next = t
            tail = tail.Next
        }
    }
    // Splice the two linked lists together
    tail.Next = nil
    head.Next = tailDummy.Next
    return headDummy.Next
}
```

Use cases for dummy nodes

> Use a dummy node when the head node is uncertain.

### [sort-list](https://leetcode-cn.com/problems/sort-list/)

> Sort a linked list in *O*(*n* log *n*) time complexity and constant space complexity.

Idea: merge sort, with finding the midpoint and merge operations.

```go
func sortList(head *ListNode) *ListNode {
    // Idea: merge sort, with finding the midpoint and merge operations
    return mergeSort(head)
}
func findMiddle(head *ListNode) *ListNode {
    // 1->2->3->4->5
    slow := head
    fast := head.Next
    // The fast pointer reaches nil first
    for fast !=nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
    }
    return slow
}
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode {
    dummy := &ListNode{Val: 0}
    head := dummy
    for l1 != nil && l2 != nil {
        if l1.Val < l2.Val {
            head.Next = l1
            l1 = l1.Next
        } else {
            head.Next = l2
            l2 = l2.Next
        }
        head = head.Next
    }
    // Connect the remaining unprocessed nodes of l1
    for l1 != nil {
        head.Next = l1
        head = head.Next
        l1 = l1.Next
    }
    // Connect the remaining unprocessed nodes of l2
    for l2 != nil {
        head.Next = l2
        head = head.Next
        l2 = l2.Next
    }
    return dummy.Next
}
func mergeSort(head *ListNode) *ListNode {
    // If there is only one node, return that node directly
    if head == nil || head.Next == nil{
        return head
    }
    // find middle
    middle := findMiddle(head)
    // Disconnect the middle node
    tail := middle.Next
    middle.Next = nil
    left := mergeSort(head)
    right := mergeSort(tail)
    result := mergeTwoLists(left, right)
    return result
}
```

Points to note

- For fast and slow pointers, check whether fast and fast.Next are nil
- In recursive mergeSort, you need to disconnect the middle node
- The recursion base case is when head is nil or head.Next is nil

### [reorder-list](https://leetcode-cn.com/problems/reorder-list/)

> Given a singly linked list *L*: *L*→*L*→…→*L\_\_n*→*L*
> Rearrange it into: *L*→*L\_\_n*→*L*→*L\_\_n*→*L*→*L\_\_n*→…

Idea: find the midpoint and disconnect, reverse the second half, then merge the two halves.

```go
func reorderList(head *ListNode)  {
    // Idea: find the midpoint and disconnect, reverse the second half, then merge the two halves
    if head == nil {
        return
    }
    mid := findMiddle(head)
    tail := reverseList(mid.Next)
    mid.Next = nil
    head = mergeTwoLists(head, tail)
}
func findMiddle(head *ListNode) *ListNode {
    fast := head.Next
    slow := head
    for fast != nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
    }
    return slow
}
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode {
    dummy := &ListNode{Val: 0}
    head := dummy
    toggle := true
    for l1 != nil && l2 != nil {
        // Switch between nodes
        if toggle {
            head.Next = l1
            l1 = l1.Next
        } else {
            head.Next = l2
            l2 = l2.Next
        }
        toggle = !toggle
        head = head.Next
    }
    // Connect the remaining unprocessed nodes of l1
    for l1 != nil {
        head.Next = l1
        head = head.Next
        l1 = l1.Next
    }
    // Connect the remaining unprocessed nodes of l2
    for l2 != nil {
        head.Next = l2
        head = head.Next
        l2 = l2.Next
    }
    return dummy.Next
}
func reverseList(head *ListNode) *ListNode {
    var prev *ListNode
    for head != nil {
        // Save the current head.Next node to prevent it from being overwritten after reassignment
        // State after one round: nil<-1 2->3->4
        //                       prev   head
        temp := head.Next
        head.Next = prev
        // move pre
        prev = head
        // move head
        head = temp
    }
    return prev
}
```

### [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)

> Given a linked list, determine whether it has a cycle.

Idea: fast and slow pointers. If the fast and slow pointers become equal, there is a cycle. Proof: if there is a cycle, the distance between the fast and slow pointers decreases by 1 with each step.
![fast_slow_linked_list](https://cdn.jsdelivr.net/gh/greyireland/algorithm-pattern@master/images/fast_slow_linked_list.png)

```go
func hasCycle(head *ListNode) bool {
    // Idea: fast and slow pointers. If the fast and slow pointers become equal, there is a cycle. Proof: if there is a cycle, the distance between the fast and slow pointers decreases by 1 with each step
    if head == nil {
        return false
    }
    fast := head.Next
    slow := head
    for fast != nil && fast.Next != nil {
        // Compare whether the pointers are equal (do not compare by val!)
        if fast == slow {
            return true
        }
        fast = fast.Next.Next
        slow = slow.Next
    }
    return false
}
```

### [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

> Given a linked list, return the first node where the cycle begins. If the list has no cycle, return `null`.

Idea: fast and slow pointers. After the fast and slow pointers meet, move the slow pointer back to the head, then move the fast and slow pointers together at the same pace; the meeting point is the entry of the cycle.
![cycled_linked_list](https://cdn.jsdelivr.net/gh/greyireland/algorithm-pattern@master/images/cycled_linked_list.png)

```go
func detectCycle(head *ListNode) *ListNode {
    // Idea: fast and slow pointers. After the fast and slow pointers meet, move the slow pointer back to the head, then move the fast and slow pointers together at the same pace; the meeting point is the entry of the cycle
    if head == nil {
        return head
    }
    fast := head.Next
    slow := head

    for fast != nil && fast.Next != nil {
        if fast == slow {
            // The slow pointer starts moving again from the head, and the fast pointer starts moving from the node after the first meeting point
            fast = head
            slow = slow.Next // note
            // Compare the pointer objects (do not compare the pointers' Val values)
            for fast != slow {
                fast = fast.Next
                slow = slow.Next
            }
            return slow
        }
        fast = fast.Next.Next
        slow = slow.Next
    }
    return nil
}
```

Pitfalls

- When comparing pointers, compare the objects directly; do not compare by value, because the list may contain duplicate values
- After the first meeting, the fast pointer needs to start from the next node and move at a constant pace together with the head pointer

Another approach is fast=head, slow=head

```go
func detectCycle(head *ListNode) *ListNode {
    // Idea: fast and slow pointers. After the fast and slow pointers meet, one of the pointers returns to the head, then both move together at the same pace; the meeting point is the entry of the cycle
    // nb+a=2nb+a
    if head == nil {
        return head
    }
    fast := head
    slow := head

    for fast != nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
        if fast == slow {
            // The pointer starts moving again from the head
            fast = head
            for fast != slow {
                fast = fast.Next
                slow = slow.Next
            }
            return slow
        }
    }
    return nil
}
```

The difference between these two approaches is that **fast=head.Next is generally used more often**, because it lets you know the node before the midpoint, which can be used for operations such as deletion.

- If fast is initialized to head.Next, the midpoint is at slow.Next
- If fast is initialized to head, the midpoint is at slow

### [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)

> Determine whether a linked list is a palindrome.

```go
func isPalindrome(head *ListNode) bool {
    // 1 2 nil
    // 1 2 1 nil
    // 1 2 2 1 nil
    if head==nil{
        return true
    }
    slow:=head
    // If fast is initialized to head.Next, the midpoint is at slow.Next
    // If fast is initialized to head, the midpoint is at slow
    fast:=head.Next
    for fast!=nil&&fast.Next!=nil{
        fast=fast.Next.Next
        slow=slow.Next
    }

    tail:=reverse(slow.Next)
    // Disconnect the two linked lists (need the node before the midpoint)
    slow.Next=nil
    for head!=nil&&tail!=nil{
        if head.Val!=tail.Val{
            return false
        }
        head=head.Next
        tail=tail.Next
    }
    return true

}

func reverse(head *ListNode)*ListNode{
    // 1->2->3
    if head==nil{
        return head
    }
    var prev *ListNode
    for head!=nil{
        t:=head.Next
        head.Next=prev
        prev=head
        head=t
    }
    return prev
}
```

### [copy-list-with-random-pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

> Given a linked list where each node contains an additional random pointer that can point to any node in the list or to null,
> return a deep copy of this list.

Idea: 1. use a hash table to store pointers; 2. place the copied node right after the original node.

```go
func copyRandomList(head *Node) *Node {
	if head == nil {
		return head
	}
	// Copy each node and place it right after the original
	// 1->2->3  ==>  1->1'->2->2'->3->3'
	cur := head
	for cur != nil {
		clone := &Node{Val: cur.Val, Next: cur.Next}
		temp := cur.Next
		cur.Next = clone
		cur = temp
	}
	// Handle the random pointers
	cur = head
	for cur != nil {
		if cur.Random != nil {
			cur.Next.Random = cur.Random.Next
		}
		cur = cur.Next.Next
	}
	// Separate the two linked lists
	cur = head
	cloneHead := cur.Next
	for cur != nil && cur.Next != nil {
		temp := cur.Next
		cur.Next = cur.Next.Next
		cur = temp
	}
	// Original list head: head 1->2->3
	// Cloned list head: cloneHead 1'->2'->3'
	return cloneHead
}
```

## Summary

Some points you must master for linked lists. Through the practice problems below, you will be able to handle most linked list problems with ease~

- null/nil exception handling
- dummy node
- fast and slow pointers
- inserting a node into a sorted linked list
- removing a node from a linked list
- reversing a linked list
- merging two linked lists
- finding the middle node of a linked list

## Practice

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
