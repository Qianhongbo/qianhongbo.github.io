---
title: Reverse Integer
date: 2022-02-01 10:02:27
categories: 
- Leetcode
---

## Reverse Integer

### Question

Given a signed 32-bit integer `x`, return `x` *with its digits reversed*. If reversing `x` causes the value to go outside the signed 32-bit integer range $[-2^{31}, 2^{31} - 1]$, then return $0$.

**Example 1:**

```
Input: x = 123
Output: 321
```

**Example 2:**

```
Input: x = -123
Output: -321
```

**Example 3:**

```
Input: x = 120
Output: 21
```

### Solution

```python
# Time complexity: O(log10(n))
# Space complexity: O(1)  
class Solution:
    def reverse(self, x: int) -> int:
        result = 0
        INT_MAX = 2**31-1
        INT_MIN = -2**31
        while x != 0:
            if x > 0: 
                remain = x % 10
                x //= 10
            else: 
                remain = x % -10
                x = -(x//-10)
            if result > INT_MAX / 10 or (result == INT_MAX // 10 and remain > 7): return 0
            if result < INT_MIN / 10 or (result == INT_MIN // 10 and remain < -8): return 0
            result = result * 10 + remain
        return result
```

> python的取余和java并不一样，[一篇很好的解释文章](https://zhuanlan.zhihu.com/p/70819721)

## Palindrome Number

### Question

**Example 1:**

```
Input: x = 121
Output: true
Explanation: 121 reads as 121 from left to right and from right to left.
```

**Example 2:**

```
Input: x = -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

### Solution

```python
# Time complexity: O(log10(n))
# Space complexity: O(1)  
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if x < 0 or (x % 10 == 0 and x != 0): return False
        reversedNumber = 0
        while x > reversedNumber:
            reversedNumber = reversedNumber * 10 + x % 10
            x = x // 10
        return reversedNumber == x or x == reversedNumber // 10 
```

