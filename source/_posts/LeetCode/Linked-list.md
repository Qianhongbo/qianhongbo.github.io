---
title: Linked list
date: 2022-01-31 20:43:02
categories: 
- Leetcode
---

## Merge Tow Sorted Lists

### Question

**Example 1:**

```
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

### Solution

```python
# Time complexity: O(n + m)
# Space complexity: O(1)

# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        prehead = ListNode(-1)
        result = prehead
        while list1 and list2:
            if list1.val <= list2.val:
                result.next = list1
                list1 = list1.next
            else:
                result.next = list2
                list2 = list2.next
            result = result.next
        result.next = list1 if list1 is not None else list2
        return prehead.next
```

