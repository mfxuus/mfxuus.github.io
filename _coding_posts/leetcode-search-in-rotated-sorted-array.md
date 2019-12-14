---
title: "[Algorithm] Search in Rotated Sorted Array"
permalink: /coding/leetcode/search-in-rotated-sorted-array/
excerpt: "LeetCode coding challenge. Easy. Algorithm. Remove Element."
classes: wide
---

- LeetCode
- Algorithm
- Medium
- [Link to Question](https://leetcode.com/problems/search-in-rotated-sorted-array/)


```python

# Is this cheating?
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        try:
            return nums.index(target)
        except:
            return -1


# Let's try to implement something else
# We have knowledge that the list is made up of 2 ascending parts
# so the last element is the largest element in the second part
class Solution:
    def search(self, nums, target):
        if not nums:
            return -1

        low = 0
        high = len(nums) - 1
        # essentially a binary search with a twist
        while low <= high:
            mid = (low + high) // 2
            if target == nums[mid]:
                return mid

            # if low and mid on same part
            if nums[low] <= nums[mid]:
                # if target in between
                if nums[low] <= target and target <= nums[mid]:
                    high = mid - 1
                else:
                    low = mid + 1
            # else, low and mid not on same part, must be low on the larger part
            # mid and high must be on same part then
            else:
                if nums[mid] <= target <= nums[high]:
                    low = mid + 1
                else:
                    high = mid - 1

        return -1
```