---
title: "[Algorithm] Two Sum"
permalink: /coding/leetcode/two-sum/
excerpt: "LeetCode coding challenge. Easy. Algorithm. Two Sum."
classes: wide
---

- LeetCode
- Algorithm
- from [Blind Curated 75](https://leetcode.com/list/xoqag3yj/)
- Easy
- [Link to Question](https://leetcode.com/problems/two-sum)


```python
class Solution:
    def twoSum(self, nums: 'List[int]', target: 'int') -> 'List[int]':
        original_list = nums
        sorted_list = sorted(nums)
        for i in range(len(sorted_list)):
            for j in range(len(sorted_list)):
                k = len(sorted_list) -1 - j
                sums = sorted_list[i] + sorted_list[k]
                if sums < target:
                    break
                elif sums > target:
                    pass
                else:
                    if sorted_list[i] != sorted_list[k]:
                        return [
                            original_list.index(sorted_list[i]),
                            original_list.index(sorted_list[k]),
                        ]
                    else:
                        a = original_list.index(sorted_list[i])
                        original_list.pop(a)
                        b = original_list.index(sorted_list[i])
                        return [a, b+1]
```

