# Data Structures

There are numerous types of data structures, generally built upon simpler primitive data types. Data structures are like different ways of arranging and storing information in a computer so that it can be used efficiently. Below are the 8 most common types.

Operations on Data Structures are the following:

- Access and read values
- Search for an arbitrary value
- Insert values at any point into the structure
- Delete values in the structure

## Arrays

An array is a collection of items stored at contiguous memory locations. The idea is to store multiple items of the same type together. This makes it easier to calculate the position of each element by simply adding an offset to a base value, i.e., the memory location of the first element of the array (generally denoted by the name of the array).

| Operation    | Time complexity |
| ------------ | --------------- |
| `Insert End` | $O(1)$          |
| `Remove End` | $O(1)$          |
| `Insert Mid` | $O(1)$          |
| `Remove Mid` | $O(1)$          |

Java example:

```java
// Arrays in java are homogeneous, containing the same type.
String[] cars = {"Volvo", "BMW", "Ford", "Mazda"};
```

Python example:

```python
# Arrays in python are `list` which are heterogeneous (mixed) containing
# any type of value.
cars = ["Ford", "Volvo", 1] # 0, 1, 2
print(cars[0]) # Ford
```

## Linked List

A linked list is a linear data structure, in which the elements are not stored at contiguous memory locations. The elements in a linked list are linked using pointers, the first node is known as the head of the list and the last is known as the tail.

| Operation    | Time complexity |
| ------------ | --------------- |
| `Insert End` | $O(1)$          |
| `Remove End` | $O(1)$          |
| `Insert Mid` | $O(1)$          |
| `Remove Mid` | $O(1)$          |

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
# Implementing a linked list is quite long and not used often
# Java for example, provides an implementation of `LinkedList`.
class Node:
    """
    An object for storing a single node of  linked list.
    Models two attributes - item and link to the next node in the list.
    """
    data = None
    next_node = None

    def __init__(self, data):
        self.data = data

    def __repr__(self):
        return "Node data: %s" % self.data


class LinkedList:
    """
    Singly linked list
    """

    def __init__(self):
        self.head = None

    def is_empty(self):
        return self.head is None

    def size(self):
        """
        Get number of nodes in the list takes O(n) linear time.
        :return: number of nodes in the list
        """
        count = 0
        current = self.head
        while current:
            count += 1
            current = current.next_node
        return count

    def add(self, data):
        """
        Adds a new node containing data at the head of the list, takes O(1) constant time.
        :param data:
        :return:
        """
        new_node = Node(data)
        new_node.next_node = self.head
        self.head = new_node


##
Node1 = Node(1)
print(Node1)  # Node data: 1
Node2 = Node(2)
Node1.next_node = Node2
print(Node1.next_node)  # Node data: 2
##
lList = LinkedList()
llNode1 = Node(1)
lList.head = llNode1
lList.size()  # 1
##
addList = LinkedList()
addList.add(1)
addList.size()  # 1
```

## HashMaps

A HashMap stores items in "key/value" pairs, one object is used as a key (index) to another object (value). It makes it easy to find values that are associated with their keys. There is no difference between a dictionary and a hashmap.

| Operation | Time complexity |
| --------- | --------------- |
| `Search`  | $O(1)$          |
| `Insert`  | $O(1)$          |
| `Remove`  | $O(1)$          |

Java example:

```java
import java.util.HashMap;

// Create a HashMap object
HashMap<String, String> cars = new HashMap<String, String>();
// Add keys and values
cars.put("Volvo", "xc40");
cars.put("Ford", "Mustang");
cars.put("BMW", "8 Series");
System.out.println(cars);
```

Python example:

```python
# Dictionary is a HashMap
cars = {
    "Volvo": "xc40",
    "Ford": "Mustang",
    "BMW": "8 Series",
}

cars.get("Volvo") # Get the key and return the value e.g. xc40
cars.get("BMW", "Vroom") # Get the key, if not found return default value e.g. Vroom
```

> [!NOTE]
> **Collision**
> A situation where two or more key objects produce the same final hash value.

## Queues

All new elements are added to the end of the queue, while the first element to be retrieved from the queue is the first added element. Queue follows the First In First Out (FIFO) rule - the item that goes in first is the item that comes out first.

| Operation    | Time complexity |
| ------------ | --------------- |
| `Push Front` | $O(1)$          |
| `Pop Front`  | $O(1)$          |
| `Push Back`  | $O(1)$          |
| `Pop Back`   | $O(1)$          |

## Binary Trees / Tree Map

Each node will have a key value, that will be mapped to the true value of the node. At a glance, it is a less efficient version of a HashMap. The value of the key of the left sub-tree, is less than the value of its parent (root) node's key. The value of the key on the right sub-tree os greater than or equal to the value of its parent (root) node's key.
The benefit of using a Binary Tree over a HashMap, is that the values are ordered.

| Operation | Time complexity   |
| --------- | ----------------- |
| `Insert`  | $O(log\text{ }n)$ |
| `Remove`  | $O(log\text{ }n)$ |
| `Search`  | $O(log\text{ }n)$ |

## Trie / Prefix Tree

TBA.

| Operation | Time complexity |
| --------- | --------------- |
| `Insert`  | $O(n)$          |
| `Searc`   | $O(n)$          |

## Heap

A tree based data structure where parent nodes have greater than or equal to priority as their child nodes.

- Max-Heap: In this heap, the value of the root node must be the greatest among all its child nodes and the same thing must be done for its left and right sub-tree also.
- Min-Heap: In this heap, the value of the root node must be the smallest among all its child nodes and the same thing must be done for its left and right sub-tree also.

> [!NOTE] > **Priority Queue**
> An abstract data type where elements have "priority" and higher priority elements are severed first. Most common implementation is the Heap.

| Operation   | Time complexity   |
| ----------- | ----------------- |
| `top()`     | $O(1)$            |
| `insert()`  | $O(log\text{ }n)$ |
| `remove()`  | $O(log\text{ }n)$ |
| `heapify()` | $O(n)$            |

## Graphs

TBA.

---

#coding #data-structure
