---
title: "[Algorithm] Merge Two Sorted Lists"
permalink: /coding/leetcode/merge-two-sorted-lists/
excerpt: "LeetCode coding challenge. Easy. Algorithm. Merge Two Sorted Lists."
classes: wide
---

- LeetCode
- Algorithm
- from [Blind Curated 75](https://leetcode.com/list/xoqag3yj/)
- Easy
- [Link to Question](https://leetcode.com/problems/merge-two-sorted-lists/)

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        # multiple assignment, so we can do the return at the end
        start = cur = ListNode(0)
        while l1 and l2:
            if l1.val >= l2.val:
                cur.next = l2
                l2 = l2.next
            else:
                cur.next = l1
                l1 = l1.next

            cur = cur.next
        # either l1 or l2 is done
        if l1:
            cur.next = l1
        else:
            cur.next = l2
        # done
        return start.next


# Other solutions I found interesting:
class Solution:
    # A recursive solution
    def mergeTwoLists(self, a, b):
        if a and b:
            if a.val > b.val:
                a, b = b, a
            a.next = self.mergeTwoLists(a.next, b)
        return a or b
```
