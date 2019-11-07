---
title: "[Algorithm] Between Two Sets"
permalink: /coding/hackerrank/between-two-sets/
excerpt: "Hackerrank coding challenge. Easy. Algorithm. Between Two Sets."
classes: wide
---

- Hackerrank
- Algorithm
- Easy
- [Link to Question](https://www.hackerrank.com/challenges/between-two-sets/problem)


```python
def getTotalX(a, b):
    # Write your code here
    # looking at the constraints, we should be able to brute force it
    # (might / probably exist better solutions...)
    works = []
    for i in range(max(a), min(b)+1):
        skip = False
        for j in a:
            if i % j != 0:
                skip = True
                break
        if not skip:
            for j in b:
                if j % i != 0:
                    skip = True
                    break
        if not skip:
            works.append(i)

    return len(works)
```