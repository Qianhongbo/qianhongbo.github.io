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

## Sum of Linked Lists

### Question

```python
linkedListOne = 2 -> 4 -> 7 -> 1
linkedListTwo = 9 -> 4 -> 5

# Output
// linkedListOne represents 1742
// linkedListTwo represents 549
// 1742 + 549 = 2291
1 -> 9 -> 2 -> 2
```

### Solution

```python
# This is an input class. Do not edit.
class LinkedList:
    def __init__(self, value):
        self.value = value
        self.next = None


# O(max(m, n)) time | O(max(m, n)) space
# For time and space, max(m, n) + 1 (1 is for carry).
# We have to store a new linked list for space.
def sumOfLinkedLists(linkedListOne, linkedListTwo):
	carry = 0
	resultLinkedList = LinkedList(0)
	currentNode = resultLinkedList
	NodeOne = linkedListOne
	NodeTwo = linkedListTwo
	
    while NodeOne is not None or NodeTwo is not None or carry != 0:
		valueOne = NodeOne.value if NodeOne is not None else 0
		valueTwo = NodeTwo.value if NodeTwo is not None else 0
		sumVal = valueOne + valueTwo + carry
		
		carry = sumVal // 10
		resultVal = sumVal % 10
		
		newNode = LinkedList(resultVal)
		currentNode.next = newNode
		currentNode = currentNode.next
		
		NodeOne = NodeOne.next if NodeOne is not None else None
		NodeTwo = NodeTwo.next if NodeTwo is not None else None
    return resultLinkedList.next
```

## Remove Kth Node From End

### Question

```python
head = 0 -> 1 -> 2 -> 3 -> 4 -> 5 -> 6
k = 2

# Output
0 -> 1 -> 2 -> 3 -> 4 -> 6
```

### Solution

```python
# This is an input class. Do not edit.
class LinkedList:
    def __init__(self, value):
        self.value = value
        self.next = None


# O(n) time | O(1) space
def removeKthNodeFromEnd(head, k):
    pointerOne = head
	pointerTwo = head
	
	for i in range(k):
		pointerTwo = pointerTwo.next
	
  # have to check this, because in the while loop
  # we need to use pointerTwo.next
  # we have to check whether pointerTwo is None...
	if pointerTwo is None:
		pointerOne.value = pointerOne.next.value
		pointerOne.next = pointerOne.next.next
		return
	
	while pointerTwo.next is not None:
		pointerOne = pointerOne.next
		pointerTwo = pointerTwo.next
	pointerOne.next = pointerOne.next.next
```

