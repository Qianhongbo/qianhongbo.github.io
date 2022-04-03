---
title: Two pointers
date: 2022-02-26 15:29:37
categories: 
- Leetcode
---

## Trap Rain Water(LeetCode 42)

### Question

Given `n` non-negative integers representing an elevation map where the width of each bar is `1`, compute how much water it can trap after raining. 

![](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)

```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

### Solution

#### Brute force

For every position, find `left_max` and `right_max`.

```python
# O(n^2) time | O(1) space
class Solution:
    def trap(self, height: List[int]) -> int:
      	if height is None:
          return 0
        ans = 0
        for i in range(len(height)):
            left_max, right_max = 0, 0
            for j in range(i, -1, -1):
                left_max = max(left_max, height[j])
            for k in range(i, len(height)):
                right_max = max(right_max, height[k])
            ans += min(left_max, right_max) - height[i]
        return ans
```

#### Dynamic programming

We can find that we don't need to find `left_max` and `right_max` at every position. We can traverse the list and store them. Although we improved the time complexity, we use more space than brute force. This is also not a good way.

![](https://leetcode.com/problems/trapping-rain-water/Figures/42/trapping_rain_water.png)

```python
# O(n) time | O(n) space
class Solution:
    def trap(self, height: List[int]) -> int:
        if height is None:
            return 0
        ans = 0
        theSize = len(height)
        left_max, right_max = [height[0]] * theSize, [height[theSize - 1]] * theSize
        for i in range(1, theSize):
            left_max[i] = max(height[i], left_max[i - 1])
        for j in range(theSize - 2, -1, -1):
            right_max[j] = max(height[j], right_max[j + 1])
        for k in range(1, theSize - 1):
            ans += min(left_max[k], right_max[k]) - height[k]
        return ans
```

#### Two pointers

The best way for this question is using two pointers, which is also a good way for some array questions. If we don't want to preserve the data like in dynamic programming way, we can find a way to get the `left_max` and `right_max` during the for loop.

Firstly, set the `leftId` and `rightId` as 0, and size. Then, set the `left_max` and `right_max` as leftmost value and rightmost value. We should know that we only use the minimum of `left_max` and `right_max`, so we should start with smaller side. When we found that the current `left_max` or `right_max` is larger than another one. We should change to another side. This ensures that we always use the minimun of `left_max` and `right_max`.

```python
# O(n) time | O(1) space
class Solution:
    def trap(self, height: List[int]) -> int:
        if not height:
            return 0
        left, right = 0, len(height) - 1
        left_max, right_max = height[left], height[right]
        water = 0
        while left <= right:
            if left_max < right_max:
                left_max = max(left_max, height[left])
                water += left_max - height[left]
                left += 1
            else:
                right_max = max(right_max, height[right])
                water += right_max - height[right]
                right -= 1
        return water
```

#### Using stacks

This way is not the best and also not easy to understand. The main idea is to track and compute the `V` shape, because it is only possible to trap vater in `V` shape.

```python
class Solution:
    def trap(self, heights: List[int]) -> int:
        stack, trapped_water = [], 0

        for hi_index, height in enumerate(heights):

            while stack and height >= heights[stack[-1]]:
                ground_height = heights[stack.pop()]
                if not stack:
                     continue
                lo_index = stack[-1]
                water_line = min(heights[lo_index], height)
                trapped_water += (water_line - ground_height) * (hi_index - lo_index - 1)

            stack.append(hi_index)

        return trapped_water
```

## Merge Sorted Array

### Question

You are given two integer arrays `nums1` and `nums2`, sorted in **non-decreasing order**, and two integers `m` and `n`, representing the number of elements in `nums1` and `nums2` respectively.

**Merge** `nums1` and `nums2` into a single array sorted in **non-decreasing order**.

The final sorted array should not be returned by the function, but instead be stored inside the array `nums1`. To accommodate this, `nums1` has a length of `m + n`, where the first `m` elements denote the elements that should be merged, and the last `n` elements are set to `0` and should be ignored. `nums2` has a length of `n`.

```
Input: nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: [1,2,2,3,5,6]
```

### Solution

#### Brute Force: Merge and sort

```python
# O((n+m)log(n+m)) time | O(n) space
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        # Write the elements of num2 into the end of nums1.
        for i in range(n):
            nums1[i + m] = nums2[i]
        # Sort nums1 list in-place.
        nums1.sort()
```

#### Three Pointers (Start from the beginning)

```python
# O(n+m) time | O(m) space
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        # must copy the nums1[:m] first...
        # can't use nums1_copy = nums1, because it will be alias
        nums1_copy = nums1[:m]
        idx1 = 0
        idx2 = 0
        for i in range(m + n):
            # have to put idx2 >= n before... need to check it first
            # or the list will be out of the index...
            if idx2 >= n or (idx1 < m and nums1_copy[idx1] <= nums2[idx2]):
                nums1[i] = nums1_copy[idx1]
                idx1 += 1
            else:
                nums1[i] = nums2[idx2]
                idx2 += 1
```

### Three Pointers (Start from the end)

Approach 2 already demonstrates the best possible time complexity, but still uses additional space. This is because the elements of array `nums1` have to be stored somwhere so that they aren't overwritten.

So, if instead we start to overwrite `nums1` from the end, we can solve this problem.

> **Interview Tip**: Whenever you're trying to solve an array problem in-place, always consider the possibility of iterating backwards instead of forwards through the array. It can completely change the problem, and make it a lot easier.

```python
# O(n+m) time | O(1) space
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:   
        idx1 = m - 1
        idx2 = n - 1
        for i in range(m + n - 1, -1, -1):
            if idx2 < 0:
                break
            if idx1 >= 0 and nums1[idx1] > nums2[idx2]:
                nums1[i] = nums1[idx1]
                idx1 -= 1
            else:
                nums1[i] = nums2[idx2]
                idx2 -= 1
```

