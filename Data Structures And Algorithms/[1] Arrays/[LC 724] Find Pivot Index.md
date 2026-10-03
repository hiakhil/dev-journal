# 724. Find Pivot Index

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/find-pivot-index/description/)

## Problem Description

Find the smallest zero-based index in `nums` whose entries before it and entries after it have equal sums. Exclude the value at the candidate index from both sums.

An empty side has sum `0`, so either end of the array can be a pivot. Return `-1` if no index satisfies the condition.

## Examples

Consider the following inputs and outputs; explanations are paraphrased.

### Example 1

```text
Input: nums = [1,7,3,6,5,6]
Output: 3
```

**Explanation:** At index `3`, the left side sums to `1 + 7 + 3 = 11`, and the right side sums to `5 + 6 = 11`. No earlier index balances both sides.

### Example 2

```text
Input: nums = [1,2,3]
Output: -1
```

**Explanation:** None of the three indices has equal sums on both sides.

### Example 3

```text
Input: nums = [2,1,-1]
Output: 0
```

**Explanation:** At index `0`, the empty left side sums to `0`, and the right side sums to `1 + (-1) = 0`.

## Constraints

- `1 <= nums.length <= 10^4`
- `-1000 <= nums[i] <= 1000`

## Solution Approach: Total Sum and Running Left Sum

Compute `totalSum` once. While checking an index, maintain `leftSum`, the sum of entries strictly before it. The sum strictly after it is then:

```text
rightSum = totalSum - leftSum - nums[index]
```

1. Sum all entries into `totalSum` and initialize `leftSum` to `0`.
2. Visit indices from left to right. Compute `rightSum` by subtracting the left side and the current value from the total.
3. If `leftSum == rightSum`, return the current index immediately.
4. Otherwise, add the current value to `leftSum` before checking the next index.
5. Return `-1` if the scan ends without finding a pivot.

Check equality before adding the current value to `leftSum`, since the pivot itself belongs to neither side. The input remains unchanged, and no prefix-sum array or slices are needed.

### Walkthrough

For `nums = [1,7,3,6,5,6]`, `totalSum = 28`.

| Index | Value | Left sum | Right sum | Equal? |
| --- | --- | --- | --- | --- |
| 0 | 1 | 0 | 27 | No |
| 1 | 7 | 1 | 20 | No |
| 2 | 3 | 8 | 17 | No |
| 3 | 6 | 11 | 11 | Yes |

Return `3` as soon as the sums match.

### Why This Works

Before each index is checked, `leftSum` is exactly the sum of all preceding entries. This is initially true because there are no entries before index `0`. Adding the current value only after the check preserves the invariant for the next index.

The total consists of the left side, the current entry, and the right side, so subtracting the first two gives the exact right-side sum. Equality therefore holds precisely when the current index is a pivot. Because indices are checked in increasing order, the first match is the leftmost pivot. If there is no match, every index has been ruled out, making `-1` correct.

A single-element array returns `0` because both sides are empty. All-zero arrays also return `0`, even though every index qualifies. Negative values, pivots at either end, and arrays without a pivot follow the same rule. Every sum has absolute value at most `10,000,000`, safely within Java's `int` range.

## Time and Space Complexity

Let `n` be the length of `nums`. Both implementations have the same bounds.

- **Time: O(n)** — Computing the total takes one pass, and checking candidate indices takes at most one more pass.
- **Auxiliary space: O(1)** — Only scalar sums, the current index, and the current value are stored. No additional arrays or slices are allocated.

## Java 21 Solution

```java
class Solution {
    public int pivotIndex(int[] nums) {
        int leftSum = 0, totalSum = 0;
        for (int value : nums) {
            totalSum += value;
        }
        
        for (int index = 0; index < nums.length; index++) {
            int rightSum = totalSum - leftSum - nums[index];
            
            if (leftSum == rightSum) {
                return index;
            }

            leftSum += nums[index];
        }

        return -1;
    }
}
```

## Python 3 Solution

```python
from typing import List


class Solution:
    def pivotIndex(self, nums: List[int]) -> int:
        leftSum, totalSum = 0, sum(nums)

        for index, value in enumerate(nums):
            rightSum = totalSum - leftSum - value
            
            if leftSum == (totalSum - leftSum - value):
                return index

            leftSum += value

        return -1
```
