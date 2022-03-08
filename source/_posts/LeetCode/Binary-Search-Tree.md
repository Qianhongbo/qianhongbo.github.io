---
title: Binary Search Tree
date: 2021-12-28 17:24:37
categories: 
- Leetcode
---

## Find CLosest Value in BST

### Question

Write a function that takes in a `BST` and a target integer value and returns that closest value to that target value contained in the BST.

```python
tree = 10
			/  \
     5   15
    / \  / \
   2  5 13  22
  /       \
 1         14
target = 12

# output = 13
```

### Solution

```python
def findClosestValueInBst(tree, target):
	closest = tree.value
	currentNode = tree
	while currentNode is not None:
		if abs(target - currentNode.value) < abs(target - closest):
			closest = currentNode.value
		if target > currentNode.value:
			currentNode = currentNode.right
		else:
			currentNode = currentNode.left
	return closest


# This is the class of the input tree. Do not edit.
class BST:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
```

## BST Construction

Write a BST class for a Binary Search Tree. The class should support:

1. Inserting values
2. Removing values
3. Searching for values/ Contain

```Python
class BST:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

    # Average: O(log(n)) time | O(1) space
    # Worst: O(n) time | O(1) space
    def insert(self, value):
        currentNode = self
        while True:
            if value < currentNode.value:
                if currentNode.left is None:
                    currentNode.left = BST(value)
                    break
                else:
                    currentNode = currentNode.left
            else:
                if currentNode.right is None:
                    currentNode.right = BST(value)
                    break
                else:
                    currentNode = currentNode.right
        return self

    # Average: O(log(n)) time | O(1) space
    # Worst: O(n) time | O(1) space
    def contains(self, value):
        currentNode = self
        while currentNode is not None:
            if value < currentNode.value:
                currentNode = currentNode.left
            elif value > currentNode.value:
                currentNode = currentNode.right
            else:
                return True
        return False

    # Average: O(log(n)) time | O(1) space
    # Worst: O(n) time | O(1) space
    def remove(self, value, parrentNode=None):
        currentNode = self
        while currentNode is not None:
            if value < currentNode.value:
                parrentNode = currentNode
                currentNode = currentNode.left
            elif value > currentNode.value:
                parrentNode = currentNode
                currentNode = currentNode.right
            else:
              	# Circumstance 1
                if currentNode.left is not None and currentNode.right is not None:
                    currentNode.value = currentNode.right.getMinValue()
                    currentNode.right.remove(currentNode.value, currentNode)
                # Circumstance 2.2
                elif parrentNode is None:
                    if currentNode.left is not None:
                        currentNode.value = currentNode.left.value
                        currentNode.right = currentNode.left.right
                        currentNode.left = currentNode.left.left
                    elif currentNode.right is not None:
                        currentNode.value = currentNode.right.value
                        currentNode.left = currentNode.right.left
                        currentNode.right = currentNode.right.right
                    else:
                        pass
                # Circumstance 2.1
                elif currentNode == parrentNode.left:
                    parrentNode.left = currentNode.left if currentNode.left is not None else currentNode.right
                # Circumstance 2.1
                elif currentNode == parrentNode.right:
                    parrentNode.right = currentNode.left if currentNode.left is not None else currentNode.right
                break
        return self


def getMinValue(self):
    currentNode = self
    while currentNode.left is not None:
        currentNode = currentNode.left
    return currentNode.value
```

> There are three circumstances for remove 
>
> 1. have both left child and right child
> 2. only have left child or right child or no child
>    - have parent node
>    - don't have parent node

# Validate BST

```python
# O(n) time | O(d) space
def validateBst(tree):
    return validateBstHelper(tree, float("-inf"), float("inf"))


def validateBstHelper(tree, minValue, maxValue):
    if tree is None:
        return True
    if tree.value < minValue or tree.value >= maxValue:
        return False
    leftIsValid = validateBstHelper(tree.left, minValue, tree.value)
    rightIsValid = validateBstHelper(tree.right, tree.value, maxValue)
    return leftIsValid and rightIsValid
```

# BST Traversal

```
tree = 10
       / \ 
      5   15
     / \    \
    2   5    22
  /
 1
 
 inOrderTraverse: [1, 2, 5, 5, 10, 15, 22]
 preOrderTraverse: [10, 5, 2, 1, 5, 15, 22]
 postOrderTraverse: [1, 2, 5, 5, 22, 15, 10]
```

```python
# O(n) time | O(n) space
def inOrderTraverse(tree, array):
    if tree is not None:
        inOrderTraverse(tree.left, array)
        array.append(tree.value)
        inOrderTraverse(tree.right, array)
    return array


# O(n) time | O(n) space
def preOrderTraverse(tree, array):
    if tree is not None:
        array.append(tree.value)
        preOrderTraverse(tree.left, array)
        preOrderTraverse(tree.right, array)
    return array


# O(n) time | O(n) space
def postOrderTraverse(tree, array):
    if tree is not None:
        postOrderTraverse(tree.left, array)
        postOrderTraverse(tree.right, array)
        array.append(tree.value)
    return array
```

# Min Height BST

Write a function that takes in a sorted array of distince integers. Construct a BST from the integers, and return the root of the BST.

Recursive!

1. Use the insert function of Class BST
2. Write the insert function by ourselves

```python
# O(nlogn) time | O(n) space
# insert O(logn) time * n elements
def minHeightBst(array):
    return constructMinHeightBst(array, None, 0, len(array) - 1)


def constructMinHeightBst(array, bst, startIdx, endIdx):
    if startIdx > endIdx:
        return
    midIdx = (startIdx + endIdx) // 2
    midValue = array[midIdx]
    if bst is None:
        bst = BST(midValue)
    else:
        bst.insert(midValue)

    constructMinHeightBst(array, bst, startIdx, midIdx - 1)
    constructMinHeightBst(array, bst, midIdx + 1, endIdx)
    return bst
```

In order to optimize the program, the built-in insert function is not practical, and there is no need to start from the root node. Directly compare and insert.

```python
# O(n) time | O(n) space
def minHeightBst(array):
    return constructMinHeightBst(array, None, 0, len(array) - 1)


def constructMinHeightBst(array, bst, startIdx, endIdx):
    if startIdx > endIdx:
        return
    midIdx = (startIdx + endIdx) // 2
    newBstNode = BST(array[midIdx])
    if bst is None:
        bst = newBstNode
    else:
      # Don't need to traverse elements from the root node.
        if bst.value > array[midIdx]:
            bst.left = newBstNode
            bst = bst.left
        else:
            bst.right = newBstNode
            bst = bst.right

    constructMinHeightBst(array, bst, startIdx, midIdx - 1)
    constructMinHeightBst(array, bst, midIdx + 1, endIdx)
    return bst
```

Make a few changes to the above code.

```python
def constructMinHeightBst(array, startIdx, endIdx):
    if startIdx > endIdx:
        return None
    midIdx = (startIdx + endIdx) // 2
    bst = BST(array[midIdx])
    bst.left = constructMinHeightBst(array, startIdx, midIdx - 1)
    bst.right = constructMinHeightBst(array, midIdx + 1, endIdx)
    return bst
```

# Find Kth Largest Value in BST

```python
class BST:
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right
```

```python
# O(N) time, O(N) space
def findKthLargestValueInBst(tree, k):
    array = []
    array = reverseInorderTraverse(tree, array)
    return array[k - 1]


def reverseInorderTraverse(tree, array):
    if tree is not None:
        reverseInorderTraverse(tree.right, array)
        array.append(tree.value)
        reverseInorderTraverse(tree.left, array)
    return array
```

```python
class TreeInfo:
    def __init__(self, numberOfNodesVisited, latestVisitedNodeValue):
        self.numberOfNodesVisited = numberOfNodesVisited
        self.latestVisitedNodeValue = latestVisitedNodeValue


# O(h + k) time, O(h) space
def findKthLargestValueInBst(tree, k):
    treeInfo = TreeInfo(0, -1)
    reverseInorderTraverse(tree, k, treeInfo)
    return treeInfo.latestVisitedNodeValue


def reverseInorderTraverse(node, k, treeInfo):
    if node is None or treeInfo.numberOfNodesVisited >= k:
        return

    reverseInorderTraverse(node.right, k, treeInfo)
    if treeInfo.numberOfNodesVisited < k:
        treeInfo.latestVisitedNodeValue = node.value
        treeInfo.numberOfNodesVisited += 1
        reverseInorderTraverse(node.left, k, treeInfo)
```

