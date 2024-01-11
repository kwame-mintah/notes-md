# Data Structures

There are numerous types of data structures, generally built upon simpler primitive data types. Data structures are like different ways of arranging and storing information in a computer so that it can be used efficiently.  Below are the 8 most common types.

## Arrays

An array is a collection of items stored at contiguous memory locations. The idea is to store multiple items of the same type together. This makes it easier to calculate the position of each element by simply adding an offset to a base value, i.e., the memory location of the first element of the array (generally denoted by the name of the array).

Java example:

```java
String[] cars = {"Volvo", "BMW", "Ford", "Mazda"};
```

Python example:

```python
cars = ["Ford", "Volvo", "BMW"]
```

## Linked List

A linked list is a linear data structure, in which the elements are not stored at contiguous memory locations. The elements in a linked list are linked using pointers.


Java example:

```java
import java.util.LinkedList;

LinkedList<String> cars = new LinkedList<String>();
cars.add("Volvo");
cars.add("BMW");
cars.add("Ford");
cars.add("Mazda");
```

Python example:

```python
class Node:
    def __init__(self, item):
        self.item = item
        self.next = None


class LinkedList:
    def __init__(self):
        self.head = None
```

## HashMaps

TBA.

Python example:

```python
# Dictonary
cars = {
    "Volvo": "xc40",
    "Ford": "Mustang",
    "BMW": "8 Series",
}
```

> [!NOTE]
> **Collision**
> A situation where two or more key objects produce the same final hash value.


## Queues

TBA.

## Binary Trees / Tree Map

Each node will have a key value, that will be mapped to the true value of the node. At a glance, it is a less efficient version of a HashMap. The value of the key of the left sub-tree, is less than the value of its parent (root) node's key. The value of the key on the right sub-tree os greater than or equal to the value of its parent (root) node's key.
The benefit of using a Binary Tree over a HashMap, is that the values are ordered.

## Trie / Prefix Tree

TBA.

## Heap

A tree based data structure where parent nodes have greater than or equal to priority as their child nodes.

> [!NOTE]
> **Priority Queue**
> An abstract data type where elements have "priority" and higher priority elements are servered first. Most common implementation is the Heap.

## Graphs

TBA.

---
#coding #data-structure