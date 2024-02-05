# Algorithm

A set of steps or instructions for completing a task. Alternatively, a set of steps a program takes to finish a task. Algorithms should produce a result, meaning they should complete and not take an infinite amount of time.

> [!NOTE]
> **Algorithm**
> Clearly defined problem statement, input, and output. The steps in the algorithm need to be in a very specific order. And these steps need to be distinct, should not be able to break down into further sub-tasks. Lastly, the algorithm should produce a result.

## Big O

$Big O$ is the theoretical definition of the complexity of an algorithm as a function of the size. In simple terms, $Big O$ is a notation used to describe complexity, essentially simplifying everything into a single variable. It is often written as $O(n)$ , O meaning the order of magnitude of complexity. A function of the size, measuring complexity as the input size grows, evaluating how it performs in the worse case scenario (Upper bounds).

>Complexity is relative, it is relative to other algorithms solving the same problem and not all algorithms.

![](/attachments/geeksforgeeks_rate_of_growth_of_algorithms.png)

- $O(1)$ is read as constant time so it takes the same amount of run time in any given case.
- $O(log\text{ }n)$ or $O(In\text{ }n)$ is read as logarithmic or sub-linear runtime, as the amount increases the number of operations taken grows slowly and eventually flattens.
- $O(n)$ is read as linear time meaning the number of operations taken will be the same as $n$.
- $O(n\text{ }log\text{ }n)$ is read as quasi-linear meaning for every amount the number of operations increases but eventually flattens. Often seen in sorting algorithms e.g. merge sort.
- $O(n^2)$ is read as quadratic runtime, when you perform nested iteration, meaning having a loop in a loop, the time complexity is quadratic.

## Factorial / Combinatorial

Factorial is written as $O(n!)$ which is $n$ minus one repeated until you reach the number one.
$$n!\text{ }n(n-1)(n-2)..(2)(1)$$
For example $3!$ is: $$3 * 2 * 1 = 6 $$
The runtime for the algorithm will be represented as $O(n!)$

> [!NOTE]
> - When the calculation is not dependent on input size, it is a constant time complexity ($O(1)$).
> - When the input size is reduced by half, when iterating, handling recursion, or whatsoever, it is a logarithmic time complexity ($O(log n)$).
> - When you have a single loop within your algorithm, it is linear time complexity ($O(n)$).
> - When you have nested loops within your algorithm, meaning a loop in a loop, it is quadratic time complexity ($O(n^2)$).
> - When the growth rate doubles with each addition to the input, it is exponential time complexity ($O(2^n)$).

## Algorithmic thinking

No solution works on every problem and it is important is to clearly define what the problem set is and clarify what values count as inputs.

## Linear / Sequential search 

For linear search it can be described as a series of values and the output is a value matching the one we're looking for, it does not matter if the values have been sorted, because it progresses sequentially checking every value and complexity notation is $O(n)$. The result could be nothing, indicating the value could not be found, which is okay.

Python example:

```python
def linear_search(inputs: list, target: int):
    """
    A sequential algorithm that compares each item in the list until the
    target is found.
    :param inputs: the list
    :param target: value to find
    :return: the index position of the target if found else return None
    """
    for i in range(0, len(inputs)):
        if inputs[i] == target:
            return i
    return None
```

## Binary search

Binary search wouldn't return the target value, instead returns the position in the list were the target is and complexity notation is $O(log\text{ }n)$ logarithmic time. The input list must be sorted and returns some sort of value to indicate that the target does not exist within the input.

Python example:

```python
def binary_search(inputs: list, target: int):
    """

    :param inputs: list to search, must be sorted
    :param target: value to fine
    :return: the index location
    """
    first, last = 0, len(inputs) - 1  # constant time

    # While loop causes the algorithm to be logarithmic runtime
    while first <= last:
        # Add first and last index use floor division operator
        # down to the nearest whole number
        midpoint = (first + last) // 2
        if inputs[midpoint] == target:  # constant time
            return midpoint
        elif inputs[midpoint] < target:  # constant time
            first = midpoint + 1
        else:
            last = midpoint - 1
    return -1  # or None
```

Alternatively, recursive binary search calls itself and needs a stopping condition and the recursive function should start with the stopping condition, sometimes referred to as the base case. 

Python example:

```python
def recursive_binary_search(inputs: list, target: int) -> bool:
    if len(inputs) == 0:
        # 1st stopping condition what should happen when empty list provided
        return False
    else:
        midpoint = (len(inputs)) // 2
        if inputs[midpoint] == target:
            # 2nd stopping condition checking midpoint and target
            return True
        else:
            if inputs[midpoint] < target:
                return recursive_binary_search(inputs[midpoint + 1 :], target)
            else:
                return recursive_binary_search(inputs[:midpoint], target)
```

## Backtracking 

Backtracking can be defined as a general algorithmic technique that considers searching every possible combination in order to solve a computational problem.

In backtracking, you start with one pos­si­ble move out of many avail­able moves. You then then try to solve the prob­lem. If you are able to solve the prob­lem with the selected move then you will return the solution. Else you will back­track and select some other move and try to solve it.

If none of the moves works out then there is no solution for the problem.

## Depth-first search (DFS)

Depth-first search (DFS) is a technique used for traversing trees or graphs. Backtracking is used for traversal. In the traversal first, the deepest node is visited and then backtracks to its parent node if no sibling of that node exists

The algorithm begins at the root node and then it explores each branch before _backtracking_. By using recursion you take advantage of the fact that left and right subtrees are also trees and share the same properties.

For Binary trees, there are three types of DFS traversals.

- In-Order:
	- Start at the left subtree of the root node, then to the root node, and lastly the right subtree of the root node.
- Pre-Order:
	- Start at the root node, then the left subtree of the root node, and at last the right subtree of the root node.
- Post-Order:
	-  Start at the left subtree nodes first and then traverse to the right subtree and visit nodes in it, and lastly process the current node. 

Example binary tree: `[5,4,8,11,null,13,4,7,2,null,null,5,1]`

```
        5         
       / \        
      4     8     
     /     / \    
  11     13    4  
 /  \         / \ 
7    2       5   1
```

In-Order: 7 -> 11 -> 2 -> 4 -> 5 -> 5 -> 4 -> 1 -> 13 -> 8
Pre-Order: 5 -> 4 -> 11 -> 7 -> 2 -> 8 -> 13 -> 4 -> 5 -> 1
Post-Order: 7 -> 2 -> 5 -> 1 -> 11 -> 13 -> 4 -> 4 -> 8 -> 5

> [!NOTE]
> Time complexity: In all the traversals, we visit every node once. It takes $O(1)$ time to visit a node; hence, the time complexity of all the traversals will be $O(n)$. Thus, the time complexity is $O(n)$ for all traversals. 

Python example:

```python
def in_order(root=None):
    if not root:
        return 0
    else:
        in_order(root.left)
        print(root.val, end=" ")
        in_order(root.right)


def pre_order(root):
    if not root:
        return 0
    else:
        print(root.value, end=" ")
        pre_order(root.left)
        pre_order(root.right)


def post_order(root=None):
    if not root:
        return 0
    else:
        post_order(root.left)
        post_order(root.right)
        print(root.val, end=" ")
```

## Breadth-first search (BFS)

Level Order Traversal technique is defined as a method to traverse a Tree such that all nodes present in the same level are traversed completely before traversing the next level. Breadth First Traversal or Breadth First Search is a recursive algorithm for searching all the vertices of a graph or tree data structure.

The algorithm works as follows:

1. Start by putting any one of the graph's vertices at the back of a queue.
2. Take the front item of the queue and add it to the visited list.
3. Create a list of that vertex's adjacent nodes. Add the ones which aren't in the visited list to the back of the queue.
4. Keep repeating steps 2 and 3 until the queue is empty.

Example binary tree: `[3,9,20,null,null,15,7]`

```
  3      
 / \     
9    20  
    /  \ 
   15   7
```

The order of traversal would be: 3 -> 9 -> 20 -> 15 -> 7. Each level would be traversed starting from the top. In the example tree, there are a total of 3 levels.

> [!NOTE]
> Time Complexity: $O(n)$ where $n$ is the number of nodes in the binary tree.
> Auxiliary Space: $O(n)$ where $n$ is the number of nodes in the binary tree.

Python example:

```python
from collections import deque

# Binary Tree
def breadth_first_search(root: Optional[TreeNode]):
    queue = deque([root])
    ans = 0

    while queue:
        # do logic for current level
        for i in range(len(queue)):
            node = queue.popleft()
            # do logic
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)

    return ans


def maxDepth(root: Optional[TreeNode]) -> int:
    # Base case (stopping condition)
    if not root:
        return 0

    depth = 0  # The root node
    # Breadth-first search a.k.a (level order traversal)
    queue = deque([root])
    while queue:
        for i in range(len(queue)):
            # remove from the queue
            # note: popleft() O(1) vs pop() O(n)
            node = queue.popleft()
            if node.left:
                # Enqueue O(1) (Add) left child to queue
                queue.append(node.left)
            if node.right:
                # Enqueue O(1) (add) right child to queue
                queue.append(node.right)
        depth += 1
    print(depth)
    return depth


# Graph BFS
def breadth_first_search(graph, node):
    visited = []
    queue = deque()
    visited.append(node)
    queue.append(node)

    while queue:
        node = queue.pop(0)
        print(m, end=" ")

        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.append(neighbor)
                queue.append(neighbor)
```


---
#coding #algorithms