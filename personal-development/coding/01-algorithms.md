# Algorithm

A set of steps or instructions for completing a task. Alternatively, a set of steps a program takes to finish a task. Algorithms should produce a result, meaning they should complete and not take an infinite amount of time.

> [!NOTE]
> **Algorithm**
> Clearly defined problem statement, input, and output. The steps in the algorithm need to be in a very specific order. And these steps need to be distinct, should not be able to break down into further sub-tasks. Lastly, the algorithm should produce a result.

$Big O$ is the theoretical definition of the complexity of an algorithm as a function of the size. In simple terms, $Big O$ is a notation used to describe complexity, essentially simplifying everything into a single variable. It is often written as $O(n)$ , O meaning the order of magnitude of complexity. A function of the size, measuring complexity as the input size grows, evaluating how it performs in the worse case scenario (Upper bounds).

>Complexity is relative, it is being done relative to other algorithms solving the same problem and not all algorithms.

## Algorithmic thinking

No solution works on every problem and it is important is to clearly define what the problem set is and clarify what values count as inputs.

## Linear / Sequential search 

For linear search it can be described as a series of values and the output is a value matching the one we're looking for, it does not matter if the values have been sorted, because it progresses sequentially checking every value and complexity notation is $O(n)$. The result could be nothing, indicating the value could not be found, which is okay.

## Binary search

Binary search wouldn't return the target value, instead return the position in the list were the target is and complexity notation is $O(log\text{ }n)$. The input list must be sorted and returns some sort of value to indicate that the target does not exist within the input.

---
#coding #algorithms