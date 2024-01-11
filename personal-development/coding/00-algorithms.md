# Algorithm

A set of steps or instructions for completing a task. Alternatively, a set of steps a program takes to finish a task. Algorithms should produce a result, meaning they should complete and not take an infinite amount of time.

> [!NOTE]
> **Algorithm**
> Clearly defined problem statement, input, and output. The steps in the algorithm need to be in a very specific order. And these steps need to be distinct, should not be able to break down into further sub-tasks. Lastly, the algorithm should produce a result.

## Big O

$Big O$ is the theoretical definition of the complexity of an algorithm as a function of the size. In simple terms, $Big O$ is a notation used to describe complexity, essentially simplifying everything into a single variable. It is often written as $O(n)$ , O meaning the order of magnitude of complexity. A function of the size, measuring complexity as the input size grows, evaluating how it performs in the worse case scenario (Upper bounds).

>Complexity is relative, it is being done relative to other algorithms solving the same problem and not all algorithms.

- $O(1)$ is read as constant time meaning takes the same amount of run time in any given case.
- $O(log\text{ }n)$ or $O(In\text{ }n)$ is read as logarithmic or sub-linear runtime, as the amount increases the number of operations taken grows slowly and eventually flattens.
- $O(n)$ is read as linear time meaning the number of operations taken will be the same as $n$.
- $O(n\text{ }log\text{ }n)$ is read as quasi-linear meaning for every amount the number of operations increases but eventually flattens. Often seen in sorting algorithms e.g. merge sort.
- $O(n^2)$ is read as quadratic runtime ...

## Factorial / Combinatorial

Factorial is written as $n!$ which is $n$ minus one repeated until you reach the number one.
$$n!\text{ }n(n-1)(n-2)..(2)(1)$$
For example $3!$ is: $$3 * 2 * 1 = 6 $$
The runtime for the algorithm will be represented as $O(n!)$ 

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

Binary search wouldn't return the target value, instead return the position in the list were the target is and complexity notation is $O(log\text{ }n)$. The input list must be sorted and returns some sort of value to indicate that the target does not exist within the input.

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
    return -1 # or None
```

Alternatively, recursive binary search calls itself and always needs a stopping condition and the recursive function should start with the stopping condition, sometimes referred to as the base case. 

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
                return recursive_binary_search(inputs[midpoint + 1:], target)
            else:
                return recursive_binary_search(inputs[:midpoint], target)
```

---
#coding #algorithms