# 509. Fibonacci Number

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/fibonacci-number/)

## Problem Description

Return the Fibonacci number at index `n`. The sequence begins with `0` and `1`; each later value is obtained by adding the previous two values.

```text
F(0) = 0
F(1) = 1
F(n) = F(n - 1) + F(n - 2), for n > 1
```

Indexing starts at zero, so the sequence is `0, 1, 1, 2, 3, 5, 8, ...`.

## Examples

Consider the following inputs and outputs; explanations are paraphrased.

### Example 1

```text
Input: n = 2
Output: 1
```

**Explanation:** Add the two initial values: `1 + 0 = 1`.

### Example 2

```text
Input: n = 3
Output: 2
```

**Explanation:** The values at indices `2` and `1` are both `1`, giving `1 + 1 = 2`.

### Example 3

```text
Input: n = 4
Output: 3
```

**Explanation:** The two preceding values are `2` and `1`, giving `2 + 1 = 3`.

## Constraints

- `0 <= n <= 30`

## Solution Approach: Iteration with Two Previous Values

Only the two most recent Fibonacci numbers are needed to compute the next one. Store them in `previous` and `current`, updating them as the sequence advances.

1. If `n <= 1`, return `n` to handle both base cases.
2. Initialize `previous = 0` and `current = 1`, representing `F(0)` and `F(1)`.
3. For each index from `2` through `n`, compute `nextValue = previous + current`. Then move `current` into `previous` and `nextValue` into `current`.
4. Return `current`, which now holds `F(n)`.

Compute `nextValue` before changing either stored value. This preserves both operands needed for the addition and avoids storing the entire sequence or using recursion.

### Walkthrough

For `n = 4`, start with `previous = 0` and `current = 1`.

| Index being computed | Next value | Previous after update | Current after update |
| --- | --- | --- | --- |
| 2 | `0 + 1 = 1` | 1 | 1 |
| 3 | `1 + 1 = 2` | 1 | 2 |
| 4 | `1 + 2 = 3` | 2 | 3 |

Return `3`.

### Why This Works

The base cases return the values specified by the definition. For larger inputs, before an iteration at index `i`, `previous` holds `F(i - 2)` and `current` holds `F(i - 1)`. This is true initially at `i = 2`.

Their sum is `F(i)` by the Fibonacci recurrence. Moving the two stored values forward preserves the invariant for the next iteration. After the iteration at index `n`, `current` therefore contains `F(n)`, which is returned.

Inputs `0` and `1` skip the loop, and `2` requires exactly one iteration. At the maximum allowed input, `F(30) = 832,040`; all intermediate values also fit safely in a Java `int` and are represented exactly by Python integers.

## Time and Space Complexity

Let `n` be the requested Fibonacci index. Both implementations have the same bounds under the given constraints.

- **Time: O(n)** — For `n >= 2`, the loop performs `n - 1` constant-time iterations. The base cases take `O(1)` time.
- **Auxiliary space: O(1)** — Only a fixed number of integer variables are stored, with no array or recursive call stack.

## Java 21 Solution

```java
class Solution {
    public int fib(int n) {
        if (n <= 1) {
            return n;
        }

        int previous = 0, current = 1;

        for (int index = 2; index <= n; index++) {
            int nextValue = previous + current;

            previous = current;
            current = nextValue;
        }

        return current;
    }
}
```

## Python 3 Solution

```python
class Solution:
    def fib(self, n: int) -> int:
        if n <= 1:
            return n

        previous, current = 0, 1

        for index in range(2, n + 1):
            nextValue = previous + current
            previous = current
            current = nextValue

        return current
```
