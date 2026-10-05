# 232. Implement Queue using Stacks

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/implement-queue-using-stacks/)

## Problem Description

Implement a first in first out (FIFO) queue using only two stacks. The implemented queue should support all the functions of a normal queue (`push`, `peek`, `pop`, and `empty`).

Implement the `MyQueue` class:

- `void push(int x)` - Pushes element x to the back of the queue.
- `int pop()` - Removes the element from the front of the queue and returns it.
- `int peek()` - Returns the element at the front of the queue.
- `boolean empty()` - Returns true if the queue is empty, false otherwise.

**Notes:**

- You must use **only** standard operations of a stack, which means only `push to top`, `peek/pop from top`, `size`, and `is empty` operations are valid.
- Depending on your language, the stack may not be supported natively. You may simulate a stack using a list or deque (double-ended queue) as long as you use only a stack's standard operations.

**Follow-up:** Make each operation take amortized `O(1)` time, so a sequence of `m` operations takes `O(m)` total time.

## Examples

Consider the following inputs and outputs:

### Example 1

```text
Input:
    ["MyQueue", "push", "push", "peek", "pop", "empty"]
    [[], [1], [2], [], [], []]

Output: [null, null, null, 1, 1, false]
```

## Constraints

- `1 <= x <= 9`
- At most `100` total calls are made to `push`, `pop`, `peek`, and `empty`.
- Every call to `pop` or `peek` is valid: the queue is nonempty when called.

## Solution Approach: Two Stacks with Transfers on Demand

Use `incoming` for newly inserted values and `outgoing` for values ready to leave. Transferring every value from one stack to the other reverses their order, putting the oldest value on top of `outgoing`.

1. Initialize both stacks as empty.
2. For `push`, place the new value on top of `incoming`.
3. Before `pop` or `peek`, check `outgoing`. If it is empty, repeatedly pop from `incoming` and push onto `outgoing` until `incoming` is empty.
4. Return the top of `outgoing`, removing it only for `pop`.
5. For `empty`, check that both stacks are empty.

Transfer only when `outgoing` is empty. Its existing values are older than anything in `incoming` and must leave first. The Java implementation uses `ArrayDeque` only as a stack; Python uses the end of each list as its top. LeetCode expects the class name `MyQueue` for this design problem.

### Walkthrough

For Example 1, stacks are shown from bottom to top; queue contents are shown from front to back.

| Operation | Incoming | Outgoing | Queue | Return value |
| --- | --- | --- | --- | --- |
| `MyQueue()` | `[]` | `[]` | `[]` | `null` |
| `push(1)` | `[1]` | `[]` | `[1]` | `null` |
| `push(2)` | `[1,2]` | `[]` | `[1,2]` | `null` |
| `peek()` | `[]` | `[2,1]` | `[1,2]` | `1` |
| `pop()` | `[]` | `[2]` | `[2]` | `1` |
| `empty()` | `[]` | `[2]` | `[2]` | `false` |

The transfer during `peek()` exposes `1` without removing it from the queue.

### Why This Works

The queue's logical order is the contents of `outgoing` from top to bottom, followed by the contents of `incoming` from bottom to top. This is true initially when both stacks are empty. Pushing onto `incoming` appends the newest value to the end of that logical order.

When `outgoing` is nonempty, its top is the oldest remaining value, so inspecting or removing it implements `peek` or `pop`. When it is empty, all remaining values are in `incoming`. Transferring them reverses their stack order, placing the oldest on top of `outgoing` while preserving the queue's logical order.

Thus every operation maintains FIFO behavior, and the queue is empty exactly when both stacks are empty. Repeated peeks preserve the front value. Pushes made while `outgoing` is nonempty wait behind its older values. Duplicate values, draining the queue completely, and pushing again after it becomes empty all follow the same rules.

## Time and Space Complexity

Let `n` be the maximum number of values held in the queue during an operation sequence, and `m` be the number of operations. Both implementations have the same bounds.

- **Time: O(1) amortized per operation** — Each inserted value is pushed onto `incoming` once, transferred to `outgoing` at most once, and removed from `outgoing` at most once. Across `m` operations, the total work is `O(m)`, including amortized resizing of the underlying arrays. A single `pop` or `peek` can take `O(n)` when it triggers a transfer; `empty` takes worst-case `O(1)` time.
- **Auxiliary space: O(n)** — The two stacks hold the queue's values. Their allocated capacity is bounded by the maximum queue size, and transfers require no third collection.

## Java 21 Solution

```java
import java.util.Stack;

class MyQueue {
    private final Stack<Integer> stack1 = new Stack<>();
    private final Stack<Integer> stack2 = new Stack<>();

    public void push(int x) {
        while (!stack1.isEmpty())
            stack2.push(stack1.pop());

        stack2.push(x);

        while (!stack2.isEmpty())
            stack1.push(stack2.pop());
    }
    
    public int pop() {
        return stack1.pop();
    }
    
    public int peek() {
        return stack1.peek();
    }
    
    public boolean empty() {
        return stack1.isEmpty();
    }
}
```

## Python 3 Solution

```python
class MyQueue:
    def __init__(self) -> None:
        self.incoming: list[int] = []
        self.outgoing: list[int] = []

    def push(self, x: int) -> None:
        self.incoming.append(x)

    def pop(self) -> int:
        self._transfer_if_needed()
        return self.outgoing.pop()

    def peek(self) -> int:
        self._transfer_if_needed()
        return self.outgoing[-1]

    def empty(self) -> bool:
        return not self.incoming and not self.outgoing

    def _transfer_if_needed(self) -> None:
        if not self.outgoing:
            while self.incoming:
                self.outgoing.append(self.incoming.pop())
```
