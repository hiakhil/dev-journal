# 485. Max Consecutive Ones

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/max-consecutive-ones/description/)

## Problem Description

Given an array `nums` containing only `0` and `1`, return the length of its longest uninterrupted run of `1`s.

The entries in a run must be adjacent: a `0` separates two runs. If the array contains no `1`s, return `0`.

## Examples

The following inputs and outputs are from the linked LeetCode statement; explanations are paraphrased.

### Example 1

```text
Input: nums = [1,1,0,1,1,1]
Output: 3
```

**Explanation:** The zero separates runs of lengths `2` and `3`. The longer run is the final three entries.

### Example 2

```text
Input: nums = [1,0,1,1,0,1]
Output: 2
```

**Explanation:** The runs have lengths `1`, `2`, and `1`, so the answer is `2`.

## Constraints

- `1 <= nums.length <= 10^5`
- `nums[i]` is either `0` or `1`.

## Solution Approach: Count the Current Run

Scan the array once while maintaining two counters: `currentRun`, the number of consecutive `1`s ending at the current position, and `longestRun`, the largest run found so far.

1. Initialize both counters to `0`.
2. For each value, increment `currentRun` if the value is `1`. Update `longestRun` with the larger of its existing value and `currentRun`.
3. If the value is `0`, reset `currentRun` to `0` because the current run has ended.
4. Return `longestRun` after processing every entry.

Updating the maximum whenever a `1` is encountered ensures a run ending at the final array position is counted without a separate final check. The input array remains unchanged.

### Walkthrough

For `nums = [1,1,0,1,1,1]`:

| Index | Value | Current run | Longest run |
| --- | --- | --- | --- |
| 0 | 1 | 1 | 1 |
| 1 | 1 | 2 | 2 |
| 2 | 0 | 0 | 2 |
| 3 | 1 | 1 | 2 |
| 4 | 1 | 2 | 2 |
| 5 | 1 | 3 | 3 |

The final value of `longestRun` is `3`.

### Why This Works

After each entry is processed, `currentRun` equals the length of the all-ones suffix of the processed prefix. A `1` extends that suffix by one, while a `0` makes its length zero. Thus the counter always describes exactly the run ending at the current position.

The second counter, `longestRun`, retains the largest such length seen so far. Every run is considered as its entries are scanned, including its full length at its last `1`. Therefore, after the scan, `longestRun` is the length of the longest run anywhere in the array.

An all-zero array returns `0`; an all-one array returns its length. Single-element arrays return `0` or `1` as appropriate. Leading or trailing zeros and alternating values are handled by the same reset rule. Neither counter exceeds `100,000`, so both fit safely in a Java `int`.

## Time and Space Complexity

Let `n` be the length of `nums`. Both implementations have the same bounds.

- **Time: O(n)** — Each entry is visited once, with constant work per entry.
- **Auxiliary space: O(1)** — Only two counters and the current value are stored. Neither implementation creates an additional array or slice.

## Java 21 Solution

```java
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int currentRun = 0, longestRun = 0;

        for (int value : nums) {
            if (value == 1) {
                currentRun++;
                longestRun = Math.max(longestRun, currentRun);
            } else {
                currentRun = 0;
            }
        }

        return longestRun;
    }
}
```

## Python 3 Solution

```python
from typing import List


class Solution:
    def findMaxConsecutiveOnes(self, nums: List[int]) -> int:
        currentRun, longestRun = 0, 0

        for value in nums:
            if value == 1:
                currentRun += 1
                longestRun = max(longestRun, currentRun)
            else:
                currentRun = 0

        return longestRun
```
