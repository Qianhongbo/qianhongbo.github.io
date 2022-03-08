---
title: Kadane's Algorithm
date: 2022-02-23 10:42:03
categories: 
- Leetcode
---

## Question 

Write a function that takes in a non-empty array of integers an returns the maximum sum that can be obtained by summing up all of the integers in a non empty sunarray of the input array. A subarray must only contain adjacent numbers.

### Sample Input

```python
array = [3, 5, -9, 1, 3, -2, 3, 4, 7, 2, -9, 6, 3, 1, -5, 4]
```

### Sample Output

```python
19 // [1, 3, -2, 3, 4, 7, 2, -9, 6, 3, 1]
```

## Solution

The reason why this question is not that straightforward is that there exists negative number in the array. If the negative number is small enough, we need to discard all preceding numbers and it.

Compare the sum ending here with the number here. Save the bigger number as `maxEndingHere`. If the number here is negative and small enough, we just save the next number. 

Compare all the `maxEndingHere`, the maximum `maxSoFar` will be the final result.

```python
# O(n) time | O(1) space
def kadanesAlgorithm(array):
    """
    Find the maximum sum that can be obtained by 
    all of the integers of the subarray of input array.
    :param array: an array
    :return: the maximum sum
    """
    assert isinstance(array, list)

    maxEndingHere = array[0]
    maxSoFar = array[0]
    for i in range(1, len(array)):
        num = array[i]
        maxEndingHere = max(maxEndingHere + num, num)
        maxSoFar = max(maxSoFar, maxEndingHere)
    return maxSoFar
```

