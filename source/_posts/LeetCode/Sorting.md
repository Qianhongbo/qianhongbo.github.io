---
title: Sorting
date: 2022-02-12 15:00:18
categories: 
- Leetcode
---

## Three Number Sort

### Question

The order is an array of three distinct integers. The first array is guaranteed to only contain integers that are in the second array, and the second array represents a desired order for the integers in the first array.

```python
# sample input
array = [1, 0, 0, -1, -1, 0, 1, 1]
order = [0, 1, -1]

# sample output
[0, 0, 0, 1, 1, 1, -1, -1]
```

### Solution

#### Bucket sort

Traverse the array two times. Get the number of each elements in the order in the  first time. Set the number in the array in the second time.

```python
# O(n) time | O(1) space
def threeNumberSort(array, order):
    buckets = [0, 0, 0]

	for i in array:
		buckets[order.index(i)] += 1
		
	for i in range(len(array)):
		if i < buckets[0]: 
			array[i] = order[0]
		elif i < buckets[0] + buckets[1]:
			array[i] = order[1]
		else:
			array[i] = order[2]
			
	return array
```

#### Two pointers

Still traverse two times using two pointers.

```python
[1, 0, 0, -1, -1, 0, 1, 1]
 ^  ^      
[0, 1, 0, -1, -1, 0, 1, 1]
    ^  ^ 
[0, 0, 1, -1, -1, 0, 1, 1]
 	   ^          ^
[0, 0, 0, -1, -1, 1, 1, 1]
               ^        ^
[0, 0, 0, -1, -1, 1, 1, 1]
           ^         ^
[0, 0, 0, 1, 1, 1, -1, -1]
          ^         ^
[0, 0, 0, 1, 1, 1, -1, -1]
 ^              ^
```

```python
# O(n) time | O(1) space
def threeNumberSort(array, order):
    Idx = 0
	for i in range(len(array)):
		if array[i] == order[0]:
			array[Idx], array[i] = array[i], array[Idx]
			Idx += 1
	
	Idy = len(array) - 1
	for i in reversed(range(len(array))):
		if array[i] == order[-1]:
			array[Idy], array[i] = array[i], array[Idy]
			Idy -= 1
	
	return array
```

#### Three pointers

```python
[1, 0, 0, -1, -1, 0, 1, 1]
 ^  ^                   ^
[0, 1, 0, -1, -1, 0, 1, 1]
    ^  ^                ^      
[0, 0, 1, -1, -1, 0, 1, 1]
       ^   ^            ^   
[0, 0, 1, 1, -1, 0, 1, -1]
       ^  ^         ^  
[0, 0, 1, 1, -1, 0, 1, -1]
       ^      ^     ^  
[0, 0, 1, 1, 1, 0, -1, -1]
       ^     ^  ^  
[0, 0, 1, 1, 1, 0, -1, -1]
       ^        ^ 
                ^
[0, 0, 0, 1, 1, 1, -1, -1]
       ^        ^   ^
```

```python
# O(n) time | O(1) space
def threeNumberSort(array, order):
    Idx0, Idx1, Idx2 = 0, 0, len(array) - 1
	
	while Idx1 <= Idx2:
		value = array[Idx1]
		
		if value == order[0]:
			array[Idx0], array[Idx1] = array[Idx1], array[Idx0]
			Idx0 += 1
			Idx1 += 1
		elif value == order[1]:
			Idx1 += 1
		else:
			array[Idx1], array[Idx2] = array[Idx2], array[Idx1]
			Idx2 -= 1
			
	return array
```

## Search in Sorted Matrix

### Question

```python
matrix = [
  [1, 4, 7, 12, 15, 1000],
  [2, 5, 19, 31, 32, 1001],
  [3, 8, 24, 33, 35, 1002],
  [40, 41, 42, 44, 45, 1003],
  [99, 100, 103, 106, 128, 1004]
]

target = 44

# output = [3, 3]
```

If the matrix does not contain the integer, return [-1, -1].

### Solution

```python
def searchInSortedMatrix(matrix, target):
    """
    search the target  number in a sorted matrix

    :param matrix: a two dimensional array of distinct integers. each row is sorted; each column is sorted
    :param target: a target integer
    :return: an array of the row and column indices of the target integer
    """
    assert isinstance(matrix, list)
    assert isinstance(target, int)

    row = 0
    column = len(matrix[0]) - 1

    while row < len(matrix) and column >= 0:
        if matrix[row][column] > target:
            column -= 1
        elif matrix[row][column] < target:
            row += 1
        else:
            return [row, column]
    return [-1, -1]
```

