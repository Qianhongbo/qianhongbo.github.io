---
title: hashmap
date: 2022-03-08 09:02:07
categories: 
- Leetcode
---

## Two sum

### Question

You may assume that each input would have ***exactly\* one solution**, and you may not use the *same* element twice. You can return the answer in any order.

```
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
```

### Solution

#### Brute force

```python
# Double loop
# O(n^2) time | O(1) square
```

#### Two-pass hash table

```python
# O(n) time | O(n) square
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        hashmap = {}
        for i in range(len(nums)):
            hashmap[nums[i]] = i
        for i in range(len(nums)):
            complement = target - nums[i]
            if complement in hashmap and hashmap[complement] != i:
                return [i, hashmap[complement]] 
```

#### One-pass hash table

```python
# O(n) time | O(n) square
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        hashmap = {}
        for i in range(len(nums)):
            hashmap[nums[i]] = i
        for i in range(len(nums)):
            complement = target - nums[i]
            if complement in hashmap and hashmap[complement] != i:
                return [i, hashmap[complement]] 
```



