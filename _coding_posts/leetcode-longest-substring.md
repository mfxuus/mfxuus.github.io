---
title: "[Algorithm] Longest Substring Without Repeating Characters"
permalink: /coding/leetcode/longest-substring-without-repeating-characters/
excerpt: "LeetCode coding challenge. Medium. Algorithm. Two Sum."
classes: wide
---

- LeetCode
- Algorithm
- from [Blind Curated 75](https://leetcode.com/list/xoqag3yj/)
- Medium
- [Link to Question](https://leetcode.com/problems/longest-substring-without-repeating-characters/)



```python
# most straight forward solution:
# Start from each character and see how long it could go for every single element.
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        if len(s) <= 1:
            return len(s)
        cur_max_i = 1
        for i in range(len(s)):
            cur_max_j = 1
            for j in range(i, len(s)-1):
                if s[j+1] not in s[i:j+1]:
                    cur_max_j += 1
                else:
                    cur_max_i = max(cur_max_i, cur_max_j)
                    break
            # update overall max
            cur_max_i = max(cur_max_i, cur_max_j)
        return cur_max_i

# Turns out this logic is alright,
# but could be implemented a lot better

class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        start = 0
        maxLength = 0
        usedChar = {}
        for index, char in enumerate(s):
            # scan through the characters
            # if char is already encountered
            # AND the current "start" index of the unrepeated substring is
            # less than or equal to the last encountered "char"
            # then it means we have concluded a run, and we need to start from
            # the next possible index.
            if char in usedChar and start <= usedChar[char]:
                # the next possible starting index is
                # usedChar[char] + 1 since if start is set to anything
                # lower, we know the result would be strictly less than
                # the current max, since we are right at a duplicating point of
                # char
                start = usedChar[char] + 1
            else:
                # if char not in already used list, or if it is early on
                # and not in the current substring we are counting
                # increase the count by one (if it is larger than current max)
                maxLength = max(maxLength, index - start + 1)
            
            # record it
            usedChar[char] = index
        return maxLength

```

