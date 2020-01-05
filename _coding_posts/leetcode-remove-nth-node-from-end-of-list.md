---
title: "[Algorithm] Remove Nth Node From End of List"
permalink: /coding/leetcode/remove-nth-node-from-end-of-list/
excerpt: "LeetCode coding challenge. Medium. Algorithm. Remove Nth Node From End of List."
classes: wide
---

- LeetCode
- Algorithm
- from [Blind Curated 75](https://leetcode.com/list/xoqag3yj/)
- Medium
- [Link to Question](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

# naive approach
# faster than 74.50% of Python3 online submissions
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        # first get all nodes
        nodes = []
        cur = head
        while cur:
            nodes.append(cur)
            cur = cur.next
        # see how many we got
        tot = len(nodes)
        start = cur = ListNode(0)
        # construct nodes while skipping the Nth from the end
        for i in range(tot):
            if i != tot - n:
                cur.next = nodes[i]
                cur = cur.next
        # clean up last node (only relevant if n == 1)
        cur.next = None
        return start.next

# "clever" approach
# https://leetcode.com/problems/remove-nth-node-from-end-of-list/discuss/8802/3-short-Python-solutions
class Solution:
    def removeNthFromEnd(self, head, n):
        fast = slow = head
        # fast speed through the nodes to get to nth node
        for _ in range(n):
            fast = fast.next
        # if fast is None, simply return head.next
        # since we are skipping first element
        if not fast:
            return head.next
        # now we step more carefully
        # while fast.next exists (we haven't reached the end)
        # we step through slow (which starts at head)
        # and fast at the same time
        while fast.next:
            fast = fast.next
            slow = slow.next
        # when fast reached the last node
        # slow is n-nodes away from the end,
        # since fast had a n-node head start
        # Thus, we skip the next node, and return the modified list of nodes
        slow.next = slow.next.next
        return head
```
