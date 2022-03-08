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

