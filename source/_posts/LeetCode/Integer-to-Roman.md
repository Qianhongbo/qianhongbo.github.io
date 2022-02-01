---
title: Integer to Roman
date: 2022-01-29 21:16:39
categories: 
- Leetcode
---

## Roman To Integer

### Question

#### Example 1:

```
Input: s = "III"
Output: 3
Explanation: III = 3.
```

#### Example 2:

```
Input: s = "LVIII"
Output: 58
Explanation: L = 50, V= 5, III = 3.
```

#### Example 3:

```
Input: s = "MCMXCIV"
Output: 1994
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.
```

### Solution

#### Left-to-Right Pass

```python
# Time O(1)
# Space O(1)
values = {
    "I": 1,
    "V": 5,
    "X": 10,
    "L": 50,
    "C": 100,
    "D": 500,
    "M": 1000,
}

class Solution:
    def romanToInt(self, s: str) -> int:
        total = 0
        i = 0
        while i < len(s):
            # If this is the subtractive case.
            if i + 1 < len(s) and values[s[i]] < values[s[i + 1]]:
                total += values[s[i + 1]] - values[s[i]]
                i += 2
            # Else this is NOT the subtractive case.
            else:
                total += values[s[i]]
                i += 1
        return total
```

#### Left-to-Right Pass Improved

```python
# Time O(1)
# Space O(1)
values = {
    "I": 1,
    "V": 5,
    "X": 10,
    "L": 50,
    "C": 100,
    "D": 500,
    "M": 1000,
    "IV": 4,
    "IX": 9,
    "XL": 40, 
    "XC": 90,
    "CD": 400,
    "CM": 900
}

class Solution:
    def romanToInt(self, s: str) -> int:
        total = 0
        i = 0
        while i < len(s):
            # This is the subtractive case.
            if i < len(s) - 1 and s[i:i+2] in values:
                total += values[s[i:i+2]] 
                i += 2
            else:
                total += values[s[i]]
                i += 1
        return total
```

#### Right-to-Left Pass

```python
# Time O(1)
# Space O(1)
values = {
    "I": 1,
    "V": 5,
    "X": 10,
    "L": 50,
    "C": 100,
    "D": 500,
    "M": 1000,
}

class Solution:
    def romanToInt(self, s: str) -> int:
        total = values.get(s[-1])
        for i in reversed(range(len(s) - 1)):
            if values[s[i]] < values[s[i + 1]]:
                total -= values[s[i]]
            else:
                total += values[s[i]]
        return total
```



## Integer to Roman

### Question

#### Example 1:

```java
Input: num = 3
Output: "III"
Explanation: 3 is represented as 3 ones.
```

#### Example 2:

```java
Input: num = 58
Output: "LVIII"
Explanation: L = 50, V = 5, III = 3.
```

#### Example 3:

```java
Input: num = 1994
Output: "MCMXCIV"
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.
```

### Solution

#### Solution 1

```python
# Time O(1)
# Space O(1)
class Solution:
    def intToRoman(self, num: int) -> str:
        digits = [(1000, "M"), (900, "CM"), (500, "D"), (400, "CD"), (100, "C"), 
                  (90, "XC"), (50, "L"), (40, "XL"), (10, "X"), (9, "IX"), 
                  (5, "V"), (4, "IV"), (1, "I")]
        roman_digits = []
        for value, symbol in digits:
            if num == 0: break
            count, num = divmod(num, value) # divmod(7, 2) ----> (3, 1)
            roman_digits.append(symbol * count)
        return ''.join(roman_digits)
```



