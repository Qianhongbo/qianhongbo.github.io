---
title: Data Structure
date: 2022-02-02 10:50:06
categories: 
- Data Structure
---

## Memory

![image-20220126100207323](https://tva1.sinaimg.cn/large/008i3skNgy1gyrm73geuxj319q0u041v.jpg)

### Bit

Short for binary digit. It represents `0` and `1`.

### Byte

A group of `8` bits. For example, `01101000` is a byte.

A single byte can represent up to `256` data values($2^8$).

### Fixed-width integer

An integer represented by a fixed amount of bits. For example, a 32-bit integer is an an integer represented by `32` bits(`4` bytes), and a 64-bit integer is an integer represented by `64` bits(`8` bytes).

### Memory

- Data stored in memory is stored in bytes and, by extension, bits.
- Bytes in memory can point to other bytes in memory, so as to store references to other data.
- The amount of memory that a machine has is bounded, making it valuable to limit how much memory an algorithm takes up.
- Accessing a byte or a fixed number of bytesis an elementary operation.

## Array

The following are an array's standard operations and their corresponding time complexities:

1. Accessing a value at a given index: `O(1)`
2. Updating a value at a given index: `O(1)`
3. Inserting a value at the beginning: `O(n)`
4. Inserting a value in the middle: `O(n)`
5. Inserting a value at the end: `O(1)` for dynamic array; `O(n)` for static array
6. Removing a value at the beginning: `O(n)`
7. Removing a value in the middle: `O(n)`
8. Removing a value at the end: `O(1)`
9. Copying the array: `O(n)`

> A static array is an implementation of an array that allocates a fixed amount of memory to be used for storing the array's values. Appending values to the array therefor involves copying the entire array and allocating new memory for it. This is a linear time operation.
>
> A lot of popular programming languages like JavaScript and Python implement arrays as dynamic arrays.

##  Linked Lists

### Singly Linked Lists

A linked list node's value and next node are typically stored in `value` and `next ` properties.

```
0 -> 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> null
```

A singly linked list typically exposes its head to its user for easy access. While finding a node in a singly linked list involves traversing through all of the nodes leading up to the node in question (as opposed to instant access with an array).

1. Accessing the head: `O(1)`
2. Accessing the tail: `O(n)`
3. Accessing the middle node: `O(n)`
4. Inserting/Removing the head: `O(1)`
5. Inserting/Removing the tail: `O(n)` + `O(1)`
6. Inserting/Removing a middle node: `O(n)` + `O(1)`
7. Searching for a value: `O(n)`

### Doubly Linked List

A doubly linked list also has a pointer to the previous node in the linked list. THe previous node is typically stored in a `prev` property.

```
null <- 0 <-> 1 <-> 2 <-> 3 <-> 4 <-> 5 <-> 6 <-> 7 -> null
```

The doubly linked list typically exposes both its head an tail to its user.

1. Accessing the head: `O(1)`
2. Accessing the tail: `O(1)`
3. Accessing the middle node: `O(n)`
4. Inserting/Removing the head: `O(1)`
5. Inserting/Removing the tail:  `O(1)`
6. Inserting/Removing a middle node: `O(n)` + `O(1)`
7. Searching for a value: `O(n)`

## Hash Tables

Under the hood, a hash table uses a dynamic array of linked lists to efficiently store key/value pairs. 

```
0: (value1, key1) -> null
1: (value2, key2) -> (value3, key3) -> (value4, key4)
2: (value5, key5) -> null
3: (value6, key6) -> null
4: null
5: (value7, key7) -> (value8, key8)
6: (value9, key9) -> null
```

Hash table rely on highly optimized hash functions to minimize the number of `collisions` than occur when storing values: cases where two keys map to the same index.

1. Inserting a key/value pair: `O(1)` on average; `O(n)` in the worse case
2. Removing a key/value pair: `O(1)` on average; `O(n)` in the worse case
3. Looking up a key: `O(1)` on average; `O(n)` in the worse case

The worst-case linear time operations occur when a hash table experiences a lot of collisions, leading to long linked lists internally, which take `O(n)` time to traverse.

However, in practice and especially in coding interviews, we typically assume that the hash functions employed by hash tables are so optimized that collisions are extremely rare and constant-time operations are all but guaranteed.

## Stacks and Queues

### Stack

An array-like data structure whose elements follow the `LIFO` rule: Last In, First Out.

A stack is typically implemented with a `dynamic array`.

1. Push an element onto the stack: `O(1)`
2. Hoping an element onto the stack: `O(1)`
3. Peeking at the element on the top of the stack: `O(1)`
4. Searching for an element in the stack: `O(n)`

### Queue

An array-like data structure whose elements follow the `FIFO` rule: First In, First Out.

A queue is typically implemented with a doubly linked list.

1. Enqueuing an element into the queue: `O(1)`
2. Dequeuing an element out of the queue: `O(1)`
3. Peeking at the element ar the front of the queue: `O(1)`
4. Searching for an lement in the queue: `O(n)`

## String

Strings are stored in memory as arrays of integers, where each character in a given string is mapped to an integer via some characer encoding standard like `ASCII`.

If the character is encoded in ASCII, it requires one byte. 1 byte(256) can cover all of the English characters. 

Stri ngs behave much like normal arrays. In most programming languages(`C++` is a notable exception), strings are immutable, meaning that they can't be edited after creation. This also means that simple operations like appending a chracter to a string are more expensive than they might appear.

```python
string = "this is a string"
newString = ""

for character in string:
newString += character
```

The operation above has a time complexity of `O(n2)` where n is the length of string, because each addition of a character to newString creates an entirely new string and is itself an `O(n)` operation. 

## Graph

A collection of nodes or values called vertices that might be related; relations between vertices are called edges.

### Graph cycle

A cycle occurs in a graph when three or more vertices in the graph are connected so as to form a closed loop

- Acyclic graph: A graph that has no cycles.
- Cyclic graph: A graph that has at least one cycle.

### Directed and undirected graph

- Directed graph: they can only be reversed in one direction, which is specified.
- Undirected graph: they can be traversed in both directions.

### Connected graph

A graph is connected if for every pair of vertices in the graph, there is a path of one or more edges connecting the given vertices.

