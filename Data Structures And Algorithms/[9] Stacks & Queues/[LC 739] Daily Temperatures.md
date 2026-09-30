# 739. Daily Temperatures

![Difficulty: Medium](https://img.shields.io/badge/Difficulty-Medium-orange)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/daily-temperatures/)

## Problem Description

Given an array of integers `temperatures` represents the daily temperatures, return an array `answer` such that `answer[i]` is the number of days you have to wait after the `ith day` to get a warmer temperature. If there is no future day for which this is possible, keep `answer[i] == 0` instead.

## Examples

Consider the following inputs and outputs:

### Example 1

```text
Input: temperatures = [73,74,75,71,69,72,76,73]
Output: [1,1,4,2,1,1,0,0]
```

### Example 2

```text
Input: temperatures = [30,40,50,60]
Output: [1,1,1,0]
```

### Example 3

```text
Input: temperatures = [30,60,90]
Output: [1,1,0]
```

## Constraints

- `1 <= temperatures.length <= 10^5`
- `30 <= temperatures[i] <= 100`

## Solution Approach: Monotonic Stack of Unresolved Days

Scan from left to right while storing the indices of days that still need a warmer day. Their temperatures are non-increasing from the bottom of the stack to the top, so the most recently added unresolved day is checked first.

1. Create an `answer` array filled with zeros and an empty stack called `pendingDays`.
2. For each day, while the stack is nonempty and today's temperature is strictly greater than the temperature at its top, pop that earlier day's index.
3. Set the popped day's answer to `day - previousDay`: today is its first warmer day.
4. Push today's index after resolving all earlier days that it can satisfy.
5. Return `answer`. Any indices still on the stack have no warmer day ahead, so their initialized zeros are correct.

Store indices rather than temperatures because the answer is a distance between days. Use a strict `>` comparison so equal temperatures remain unresolved. The input is not modified.

### Walkthrough

For Example 1, the stack below is shown from bottom to top as `index:temperature`.

| Day | Temperature | Answers resolved today | Stack after pushing today |
| --- | --- | --- | --- |
| 0 | 73 | None | `[0:73]` |
| 1 | 74 | `answer[0] = 1` | `[1:74]` |
| 2 | 75 | `answer[1] = 1` | `[2:75]` |
| 3 | 71 | None | `[2:75, 3:71]` |
| 4 | 69 | None | `[2:75, 3:71, 4:69]` |
| 5 | 72 | `answer[4] = 1`, `answer[3] = 2` | `[2:75, 5:72]` |
| 6 | 76 | `answer[5] = 1`, `answer[2] = 4` | `[6:76]` |
| 7 | 73 | None | `[6:76, 7:73]` |

Days `6` and `7` remain unresolved. The result is `[1,1,4,2,1,1,0,0]`.

### Why This Works

Before each day is processed, the stack contains exactly the earlier days with no warmer day encountered yet. Their indices increase from bottom to top, while their temperatures are non-increasing. This holds initially for an empty stack.

If today's temperature exceeds the top temperature, that earlier day can now be resolved. No intervening day was warmer, or the index would already have been removed. Today is therefore its first warmer day, and the index difference is the correct wait.

After all such indices are popped, the stack is empty or its top temperature is at least today's temperature. All entries below it are at least as warm, so none can be resolved today either. Pushing today's index preserves the invariant. When the scan ends, remaining indices have no warmer future day, making their zero answers correct.

## Time and Space Complexity

Let `n` be the length of `temperatures`. Both implementations have the same bounds.

- **Time: O(n)** — Each index is pushed once and popped at most once. Although the code contains a nested loop, there are at most `n` pops across the entire scan; stack operations take amortized constant time.
- **Auxiliary space: O(n), excluding the output** — The stack can hold all `n` indices, for example when every temperature is equal.
- **Output space: O(n)** — The returned array or list contains one waiting time per day. Total space including the output remains `O(n)`.

## Java 21 Solution

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int[] stack = new int[temperatures.length];
        int[] answer = new int[temperatures.length];

        int top = -1;
        for (int i = 0; i < temperatures.length; i++) {
            while (top >= 0 && temperatures[i] > temperatures[stack[top]]) {
                answer[stack[top]] = i - stack[top];
                top -= 1;
            }

            stack[++top] = i;
        }

        return answer;
    }
}
```

## Python 3 Solution

```python
from typing import List


class Solution:
    def dailyTemperatures(self, temperatures: list[int]) -> list[int]:
        indexStack, answer = [], [0]*len(temperatures)

        for index, temperature in enumerate(temperatures):
            while len(indexStack) > 0 and temperatures[indexStack[-1]] < temperature:
                answer[indexStack[-1]] = index - indexStack[-1]
                indexStack.pop()
                
            indexStack.append(index)

        return answer
```
