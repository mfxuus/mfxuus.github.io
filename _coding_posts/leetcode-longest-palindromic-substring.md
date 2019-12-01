---
title: "[Algorithm] Longest Palindromic Substring"
permalink: /coding/leetcode/leetcode-longest-palindromic-substring/
excerpt: "LeetCode coding challenge. Medium. Algorithm. Longest Palindromic Substring."
classes: wide
---

- LeetCode
- Algorithm
- from [Blind Curated 75](https://leetcode.com/list/xoqag3yj/)
- Medium
- [Link to Question](https://leetcode.com/problems/longest-palindromic-substring/)



```python

# naive approach
class Solution:
    def check_if_palindrome(self, s):
        if s == s[::-1]:
            return True
        return False

    def longestPalindrome(self, s: str) -> str:
        l = len(s)
        # start from the longest substrings, scan through s
        # if we got a palindrome, it must be of longest length
        # so return and terminate
        for i in range(1, l+1):
            for j in range(i):
                temp_s = s[j:j+l-i+1]
                if self.check_if_palindrome(temp_s):
                    return temp_s
        return ''


# Dynamic Programming
# construct a len(s)*len(s) table
# where table[i][j] holds a boolean indicator, of whether
# s[i:j+1] is a palindrome

# Not terribly different from the naive approach,
# but saves lots of computation in the check_if_palindrome step.
class Solution:
    def longestPalindrome(self, s) -> str:
        if(len(s) == 0 or len(s) == 1):
            return s

        table = [[False]*len(s) for j in range(len(s))]
        # m stores the starting index of the palindrome
        m = None
        length = 1
        # strings of length 1 are always palindrome
        for i in range(len(s)):
            table[i][i] = True
            m = i
        # strings of length 2
        for i in range(len(s)-1):
            if(s[i] == s[i+1]):
                table[i][i+1] = True
                m = i
                length = 2
        # strings of length 3 and greater
        for l in range(3, len(s)+1):
            for i in range(len(s)-l+1):
                j = i+l-1
                # current string is palindrome if the starting character = ending character 
                # and the string in between is a palindrome which we check from the table constructed
                if s[i] == s[j] and table[i+1][j-1]:
                    table[i][j] = True
                    m = i
                    length = l
        return s[m:m+length]



# linear time, Manacher algorithm
# credit: https://leetcode.com/problems/longest-palindromic-substring/discuss/3337/Manacher-algorithm-in-Python-O(n)
class Solution:
    # Manacher algorithm
    # http://en.wikipedia.org/wiki/Longest_palindromic_substring

    def longestPalindrome(self, s):
        # Transform S into T.
        # For example, S = "abba", T = "^#a#b#b#a#$".
        # ^ and $ signs are sentinels appended to each end to avoid bounds checking
        T = '#'.join('^{}$'.format(s))
        n = len(T)
        P = [0] * n
        C = R = 0
        for i in range(1, n-1):
            P[i] = (R > i) and min(R - i, P[2*C - i]) # equals to i' = C - (i-C)
            # Attempt to expand palindrome centered at i
            while T[i + 1 + P[i]] == T[i - 1 - P[i]]:
                P[i] += 1
    
            # If palindrome centered at i expand past R,
            # adjust center based on expanded palindrome.
            if i + P[i] > R:
                C, R = i, i + P[i]
    
        # Find the maximum element in P.
        maxLen, centerIndex = max((n, i) for i, n in enumerate(P))
        return s[(centerIndex - maxLen)//2: (centerIndex  + maxLen)//2]
```

