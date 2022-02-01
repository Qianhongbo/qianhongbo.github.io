---
title: Longest Common Prefix
date: 2022-01-30 09:34:42
categories: 
- Leetcode
---

## Longest Common Prefix

### Question

**Example 1:**

```
Input: strs = ["flower","flow","flight"]
Output: "fl"
```

**Example 2:**

```
Input: strs = ["dog","racecar","car"]
Output: ""
Explanation: There is no common prefix among the input strings.
```

### Solution

#### Horizontal scanning

![Horizontal scanning](https://leetcode.com/media/original_images/14_basic.png)

```python
# Time Complexity: O(S), where S is the sum of all characters in all strings
# Space Complexity: O(1)
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        prefix = strs[0]
        for s in strs:
            while s.find(prefix) != 0:
                prefix = prefix[0:-1]
                if prefix is None: return ""
        return prefix
```

#### Vertical scanning

Imagine a very short string is the common prefix at the end of the array. The above approach will still do S*S* comparisons. One way to optimize this case is to do vertical scanning. We compare characters from top to bottom on the same column (same character index of the strings) before moving on to the next column.

```python
# Time Complexity: O(S), where S is the sum of all characters in all strings
# Even though the worst case is still the same as Approach 1, 
# in the best case there are at most n minLen comparisons 
# where minLen is the length of the shortest string in the array.
# Space Complexity: O(1)
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if strs is None or len(strs) == 0: return ""
        for i in range(len(strs[0])):
            for j in range(1, len(strs)):
                if i == len(strs[j]) or strs[j][i] != strs[0][i]:
                    return strs[0][0:i]
        return strs[0]
```

#### Divide and conquer

![Divide and conquer](https://leetcode.com/media/original_images/14_lcp_diviso_et_lmpera.png)

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        return self.longestCommonPrefixHelper(strs, 0, len(strs) - 1)
    
    def longestCommonPrefixHelper(self, strs: List[str], l: int , r: int):
        if l == r:
            return strs[l]
        else:
            mid = (l + r) // 2
            lcpLeft = self.longestCommonPrefixHelper(strs, l, mid)
            lcpRight = self.longestCommonPrefixHelper(strs, mid + 1, r)
            return self.commonPrefix(lcpLeft, lcpRight)
        
    def commonPrefix(self, left: str, right: str):
        minLen = min(len(left), len(right))
        for i in range(minLen):
            if left[i] != right[i]:
                return left[0:i]
        return left[0:minLen]
```

#### Binary search

![Binary search](https://leetcode.com/media/original_images/14_lcp_binary_search.png)

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if strs is None or len(strs) == 0: return ""
        minLen = len(strs[0])
        for s in strs:
            minLen = min(minLen, len(s))
        low = 1
        high = minLen
        while low <= high:
            middle = (low + high) // 2
            if self.isCommonPrefix(strs, middle):
                low = middle + 1
            else:
                high = middle - 1
        return strs[0][0:(low + high) // 2]
    
    
    def isCommonPrefix(self, strs: List[str], middle: int):
        str1 = strs[0][0:middle]
        for i in range(1, len(strs)):
            if not strs[i].startswith(str1): return False
        return True
```

