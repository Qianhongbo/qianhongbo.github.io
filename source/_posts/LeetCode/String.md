---
title: String
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

## Longest Palindromic Substring

### Question

```python
# Sample input 
string = "abaxyzzyxf"

# Sample output
"xyzzyx"
```

### Solution

#### Brutal force

判断是否为palindrome string是`O(n)`, 最简单的方式是获取所有的子串，对每一种子串进行验证。

```python
# O(n^3) time | O(n) space
def longestPalindromicSubstring(string):
    longest = ""
    for i in range(len(string)):
        for j in range(i, len(string)):
            substring = string[i : j + 1]
            if len(substring) > len(longest) and isPalindrome(substring):
                longest = substring
    return longest


def isPalindrome(string):
    leftIdx = 0
    rightIdx = len(string) - 1
    while leftIdx < rightIdx:
        if string[leftIdx] != string[rightIdx]:
            return False
        leftIdx += 1
        rightIdx -= 1
    return True
```

#### Better way

In order not to check all the substrings, we can change how we check the substring is palindrome. 

We can check the substring from the center. If the character on the both side of the center is the same, we can go further and stop when they are different or reach the side. But in this case, we also have two circumstances, one is an odd substring, another is an even substring.

```python
# O(n^2) time | O(n) space
def longestPalindromicSubstring(string):
    # [leftIdx, rightIdx]
    currentLongest = [0, 1]
    for i in range(1, len(string)):
        odd = getLongestPalindromeFrom(string, i - 1, i + 1)
        even = getLongestPalindromeFrom(string, i - 1, i)
        # use lambda function to check the length of substring
        # x[1] represent the rightIdx
        # x[0] represent the leftIdx
        longest = max(odd, even, key = lambda x: x[1] - x[0])
        currentLongest = max(longest, currentLongest, key = lambda x: x[1] - x[0])
    return string[currentLongest[0] : currentLongest[1]]


def getLongestPalindromeFrom(string, leftIdx, rightIdx):
    while leftIdx >= 0 and rightIdx < len(string):
        if string[leftIdx] != string[rightIdx]:
            break
        leftIdx -= 1
        rightIdx += 1
    # The reason we return this is that:
    # The last time in the while loop will fail, but we have move the pointer
    # We need to put them back, but the rightIdx also need to plus one
    return [leftIdx + 1, rightIdx]
```

## Group Anagrams

### Question

Anagrams are strings made up of exactly the same letters, where order dosen't matter.

```python
# Sample Input
words = ["yo", "act", "flop", "tac", "foo", "cat", "oy", "olfp"]

# Sample output
[["yo", "oy"], ["flop", "olfp"], ["act", "tac", "cat"], ["foo"]]
```

### Solution

- sort every word in the list
- use hash code to group the anagrams

```python
# O(wnlog(n)) time | O(wn) space
# where w is the number of words and 
# n is the length of the longest word
def groupAnagrams(words):
  anagrams = {}
	for word in words:
		sortedWord = "".join(sorted(word))
		if sortedWord in anagrams:
			anagrams[sortedWord].append(word)
		else:
			anagrams[sortedWord] = [word]
	return list(anagrams.values())
```

## Valid IP Addresses

### Question

Write a function that returns all the possible IP addresses that can be created by inserting `.`s in the string. 

A valid IP adress should have some properties:

- it consists of four sets of integer
- the individual integer is within the range`0 - 255`, inclusive
- not contain leading `0`

### Solution

```python
# O(1) time | O(1) space
def validIPAddresses(string):
    result = []
	
	for i in range(1, min(len(string), 4)):
		currentIPAddressParts = ["", "", "", ""]
		
		currentIPAddressParts[0] = string[:i]
		if not isValidPart(currentIPAddressParts[0]):
			continue
		
		for j in range(i + 1, i + min(len(string) - i, 4)):
			currentIPAddressParts[1] = string[i:j]
			if not isValidPart(currentIPAddressParts[1]):
				continue
			
			for k in range(j + 1, j + min(len(string) - j, 4)):
				currentIPAddressParts[2] = string[j:k]
				currentIPAddressParts[3] = string[k:]
				
				if isValidPart(currentIPAddressParts[2]) and isValidPart(currentIPAddressParts[3]):
					result.append(".".join(currentIPAddressParts))
	
	return result
	
def isValidPart(string):
	stringAsInt = int(string)
	if stringAsInt > 255:
		return False
	return len(string) == len(str(stringAsInt)) # check for leading 0
```

