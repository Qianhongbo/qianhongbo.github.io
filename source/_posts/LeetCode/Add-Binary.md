---
title: Add Binary
date: 2022-01-28 10:15:06
categories: 
- Leetcode
---

## Question

Given two binary strings `a` and `b`, return *their sum as a binary string*.

### Example 1:

```text
Input: a = "11", b = "1"
Output: "100"
```

### Example 2:

```text
Input: a = "1010", b = "1011"
Output: "10101"
```

## Solution

### Solution 1

```python
# O(M+N)
class Solution:
    def addBinary(self, a, b) -> str:
        return '{0:b}'.format(int(a, 2) + int(b, 2))
```

```python
# 二进制字符串转十进制
int(“11”, 2) # 3

# 把十进制数12转为对应的二进制数的字符串形式
'{0:b}'.format(12)  
'1100'
```

1 . In Java this approach is limited by the length of the input strings a and b. Once the string is long enough, the result of conversion into integers will not fit into Integer, Long or BigInteger.

2 . This method has quite low performance in the case of large input numbers.

### Solution 2

```python
# Time O(max(M,N))
# Space O(max(N,M))
class Solution:
    def addBinary(self, a, b) -> str:
        n = max(len(a), len(b))
        # zfill() 方法返回指定长度的字符串，原字符串右对齐，前面填充0
        a, b = a.zfill(n), b.zfill(n)
        
        carry = 0
        answer = []
        for i in range(n - 1, -1, -1):
            if a[i] == '1':
                carry += 1
            if b[i] == '1':
                carry += 1
            if carry % 2 == 1:
                answer.append('1')
            else:
                answer.append('0')
            carry //= 2
        # 最后一位的进位
        if carry == 1:
            answer.append('1')
        # append 方法获得的数据结果需要反向，并将字符串连接到一起
        answer.reverse()
        return ''.join(answer)
```

#### Solution 3

Here the input is more adapted to push towards Approach 1, but there is popular Facebook variation of this problem when interviewer provides you two numbers and asks to sum them up `without using addition operation`.

How to start? There is an interview tip for bit manipulation problems: if you don't know how to start, start from computing XOR for your input data. 

```python
class Solution:
    def addBinary(self, a, b) -> str:
        x, y = int(a, 2), int(b, 2)
        while y:
            answer = x ^ y
            carry = (x & y) << 1
            x, y = answer, carry
        return bin(x)[2:] # bin()将十进制转二进制
```

```python
# 结果的前两位需要去掉
>>>bin(10)
'0b1010'
```

