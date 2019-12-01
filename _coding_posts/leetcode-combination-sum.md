---
title: "[Algorithm] Combination Sum"
permalink: /coding/leetcode/leetcode-combination-sum/
excerpt: "LeetCode coding challenge. Medium. Algorithm. Combination Sum."
classes: wide
---

- LeetCode
- Algorithm
- from [Blind Curated 75](https://leetcode.com/list/xoqag3yj/)
- Medium
- [Link to Question](https://leetcode.com/problems/combination-sum/)



```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        # table = X * Y where X is length of set and Y is target (+1, so easier tracking)
        # at each index is stored a list of lists, say L, where
        # table[i][j] = L_ij = the list that contains all combos of first i elements that add up to j
        self.candidates = candidates
        self.target = target
        self.table = [[None for x in range(target+1)] for i in candidates]
        return self.checkTable(len(candidates)-1, target)

    def checkTable(self, i, j):
        candidates = self.candidates
        if i == 0:
            if j % candidates[i] == 0:
                self.table[i][j] = [[candidates[i]] * (j //candidates[i])]
                return [[candidates[i]] * (j //candidates[i])]
            else:
                self.table[i][j] = []
                return []

        # if we have already computed it, just get the value
        # slows down about 30% if we don't do this
        if self.table[i][j] is not None:
            return self.table[i][j]
        # else, we will need to do some work
        # 2 parts, with and without candidates[i]
        # WITH: table[i-1][j-candidates[i] * N]
        # WITHOUT table[i-1][j] (N == 0)
        # only with: check if % == 0
        else:
            res = []
            N = 0
            while j - candidates[i] * N > 0:
                no_i_res = self.checkTable(i - 1, j - candidates[i] * N)
                res += [x + [candidates[i]] * N for x in no_i_res]
                N += 1
            # check if can do it with only itself
            if j % candidates[i] == 0:
                res += [[candidates[i]] * (j // candidates[i])]
            self.table[i][j] = res
            return res


# optimized with backtracking approach
# https://leetcode.com/problems/combination-sum/discuss/16554/Share-My-Python-Solution-beating-98.17
class Solution(object):
    def combinationSum(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        def dfs(remain, combo, index):
            if remain == 0:
                result.append(combo)
                return
            for i in range(index, len(candy)):
                if candy[i] > remain:
                    # exceeded the sum with candidate[i]
                    break #the for loop
                
                dfs(remain - candy[i], combo + [candy[i]], i)
                
        candy = sorted(candidates)
        result = []
        dfs(target, [], 0)
        return result
```

