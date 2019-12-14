---
title: "[Algorithm] Remove Element"
permalink: /coding/leetcode/remove-element/
excerpt: "LeetCode coding challenge. Easy. Algorithm. Remove Element."
classes: wide
---

- LeetCode
- Algorithm
- Medium
- [Link to Question](https://leetcode.com/problems/remove-element/)

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        # while val is in nums, remove it from nums
        # as per the doc:
        #       list.remove(x)
        #           Remove the first item from the list whose value is x. It is an error if there is no such item.
        while val in nums:
            nums.remove(val)
```