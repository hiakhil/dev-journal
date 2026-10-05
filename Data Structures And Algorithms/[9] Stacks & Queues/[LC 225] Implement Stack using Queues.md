# 225. Implement Stack using Queues

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/implement-stack-using-queues/)

## Problem Description

Implement a last-in-first-out (LIFO) stack using only two queues. The implemented stack should support all the functions of a normal stack (`push`, `top`, `pop`, and `empty`).

Implement the `MyStack` class:

- `void push(int x)` - Pushes element `x` to the top of the stack.
- `int pop()` - Removes the element on the top of the stack and returns it.
- `int top()` - Returns the element on the top of the stack.
- `boolean empty()` - Returns `true` if the stack is empty, `false` otherwise.

**Notes:**

- You must use **only** standard operations of a stack, which means only `push to top`, `peek/pop from top`, `size`, and `is empty` operations are valid.
- Depending on your language, the stack may not be supported natively. You may simulate a stack using a list or deque (double-ended queue) as long as you use only a stack's standard operations.

**Follow-up:** Implement the stack with only one queue. The solution below meets this requirement.

## Examples

Consider the following inputs and outputs:

### Example 1

```text
Input:
    ["MyStack", "push", "push", "top", "pop", "empty"]
    [[], [1], [2], [], [], []]

Output: [null, null, null, 2, 2, false]
```

## Constraints

- `1 <= x <= 9`
- At most `100` total calls are made to `push`, `pop`, `top`, and `empty`.
- Every call to `pop` or `top` is valid: the stack is nonempty when called.

## Solution Approach: One Queue with Rotation on Push

Maintain the queue in stack order: its front is the stack's top, followed by progressively older values. Each push rearranges the queue using only front removals and back insertions.

1. Initialize an empty queue.
2. For `push(x)`, record the number of existing elements, then add `x` to the back.
3. Move exactly that many elements from the front to the back. This brings `x` to the front while preserving the relative order of the older elements.
4. For `pop`, remove and return the queue's front. For `top`, inspect the front without removing it.
5. For `empty`, check whether the queue is empty.

Save the old size before insertion so the newly added value is not rotated past the front. Do not rotate until the queue is empty: removing and immediately reinserting an element does not reduce its size. Java uses the `Queue` interface, and Python uses `deque.append`, `deque.popleft`, and front inspection with `[0]`. LeetCode expects the class name `MyStack` for this design problem.

### Walkthrough

Queue contents are shown from front to back, which is also the stack's order from top to bottom.

| Operation | Queue changes | Queue afterward | Return value |
| --- | --- | --- | --- |
| `MyStack()` | Initialize | `[]` | `null` |
| `push(1)` | Append `1`; no older elements to move | `[1]` | `null` |
| `push(2)` | Append: `[1,2]`; move `1` to the back | `[2,1]` | `null` |
| `top()` | Inspect the front | `[2,1]` | `2` |
| `pop()` | Remove the front | `[1]` | `2` |
| `empty()` | Check whether the queue is empty | `[1]` | `false` |

### Why This Works

Initially, the empty queue correctly represents the empty stack. Assume the queue currently lists stack values from top to bottom. Appending a new value places it behind all existing values. Moving each old value from front to back exactly once brings the new value to the front and leaves the old values in their original relative order. The queue therefore represents the updated stack from top to bottom.

Removing or inspecting the queue's front now removes or inspects the stack's top, and the queue is empty exactly when the stack is empty. These operations preserve the representation, so every sequence of operations follows LIFO behavior.

Pushing into an empty stack requires no rotation. Repeated `top` calls leave the contents unchanged. Duplicate values, interleaved pushes and pops, and reuse after the stack is drained work without special cases. The valid-call guarantee makes additional empty-stack handling unnecessary for `pop` and `top`.

## Time and Space Complexity

Let `n` be the number of elements immediately after a push, and `s` be the maximum number stored during an operation sequence. Both implementations have the same bounds.

- **Time: O(n) for push; O(1) for pop, top, and empty** — A push inserts one value and moves the `n - 1` older values to the back. Queue insertion is amortized constant time for Java's `ArrayDeque`; any capacity growth during a push still fits its `O(n)` bound. Python deque operations at the ends take constant time.
- **Auxiliary space: O(s)** — One queue holds the stack's values. Its allocated storage is bounded by the maximum stack size, and rotation uses no second collection.

## Java 21 Solution

```java
import java.util.ArrayDeque;
import java.util.Queue;

class MyStack {
    private final Queue<Integer> queue = new ArrayDeque<>();

    public void push(int x) {
        int previousSize = queue.size();
        queue.offer(x);

        for (int moved = 0; moved < previousSize; moved++) {
            queue.offer(queue.remove());
        }
    }

    public int pop() {
        return queue.remove();
    }

    public int top() {
        return queue.element();
    }

    public boolean empty() {
        return queue.isEmpty();
    }
}
```

## Python 3 Solution

```python
from collections import deque


class MyStack:
    def __init__(self) -> None:
        self.queue: deque[int] = deque()

    def push(self, x: int) -> None:
        previousSize = len(self.queue)
        self.queue.append(x)

        for _ in range(previousSize):
            self.queue.append(self.queue.popleft())

    def pop(self) -> int:
        return self.queue.popleft()

    def top(self) -> int:
        return self.queue[0]

    def empty(self) -> bool:
        return not self.queue
```
