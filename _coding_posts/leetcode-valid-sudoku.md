---
title: "[Algorithm] Valid Sudoku"
permalink: /coding/leetcode/leetcode-valid-sudoku/
excerpt: "LeetCode coding challenge. Medium. Algorithm. Valid Sudoku."
classes: wide
---

- LeetCode
- Algorithm
- Medium
- [Link to Question](https://leetcode.com/problems/valid-sudoku/)



```python
class Solution:
    def isValidSudoku(self, board: List[List[str]]) -> bool:
        cols = [[] for x in range(9)]
        boxes = [[] for x in range(9)]
        rows = [[] for x in range(9)]
        # construct
        for i in range(9):
            for j in range(9):
                temp = board[i][j]
                if temp != '.':
                    rows[i].append(temp)
                    cols[j].append(temp)
                    boxes[self.getBoxNum(i,j)].append(temp)
            # after each row, do a quick check
            if len(rows[i]) != len(set(rows[i])):
                return False
        # check columns and boxes
        for i in range(9):
            if len(boxes[i]) != len(set(boxes[i])):
                return False
            if len(cols[i]) != len(set(cols[i])):
                return False
        # else we good
        return True

    def getBoxNum(self, i, j):
        # just hard code it
        box_dict = {
            (1, 1): 0,
            (1, 2): 3,
            (1, 3): 6,
            (2, 1): 1,
            (2, 2): 4,
            (2, 3): 7,
            (3, 1): 2,
            (3, 2): 5,
            (3, 3): 8,
        }
        temp_i = int((3.5+i)//3)
        temp_j = int((3.5+j)//3)
        return box_dict[(temp_i, temp_j)]

```

