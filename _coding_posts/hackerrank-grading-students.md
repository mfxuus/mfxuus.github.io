---
title: "[Algorithm] Grading Students"
permalink: /coding/hackerrank/grading-students/
excerpt: "Hackerrank coding challenge. Easy. Algorithm. Grading Students."
classes: wide
---

- Hackerrank
- Algorithm
- Easy
- [Link to Question](https://www.hackerrank.com/challenges/grading/problem)


```python
def process_grade(grade):
    """
    Implement the rounding
    """
    if grade <= 37:
        return grade
    elif grade % 5 > 2:
        return ((grade // 5) + 1) * 5
    return grade

def gradingStudents(grades):
    # Write your code here
    res = []
    for grade in grades:
        res.append(process_grade(grade))

    return res
```

