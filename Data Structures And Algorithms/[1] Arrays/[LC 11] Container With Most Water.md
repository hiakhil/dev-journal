# 11. Container With Most Water

![Difficulty: Medium](https://img.shields.io/badge/Difficulty-Medium-orange)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/container-with-most-water/description/)

## Problem Description

You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `ith` line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return the **maximum amount of water a container can store**.

**Notice** that you may not slant the container.

## Examples

Consider the following inputs and outputs; explanations are paraphrased.

### Example 1

```text
    Input: 
        height = [1,8,6,2,5,4,8,3,7]
    
    Output: 49
```

**Explanation:** Choose zero-based indices `1` and `8`. Their heights are `8` and `7`, so the container has width `7` and water height `7`, giving area `49`.

### Example 2

```text
    Input: 
        height = [1,1]
    
    Output: 1
```

**Explanation:** The only pair has width `1` and water height `1`, giving area `1`.

## Constraints

- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

## Solution Approach: Two Pointers

Start with the widest possible container, using the first and last lines. After recording its area, move the pointer at the shorter line inward. That line limits the water level, and keeping it while reducing the width cannot produce a larger area.

1. Set `left = 0`, `right = height.length - 1`, and `maxArea = 0`.
2. While `left < right`, compute the current area and update `maxArea`.
3. If the left height is less than or equal to the right height, increment `left`. Otherwise, decrement `right`.
4. Return `maxArea` when the pointers meet.

When the heights are equal, either pointer can safely move; these implementations move the left one. Compute the area before moving a pointer, and use the shorter height rather than the taller one. The input remains unchanged.

### Walkthrough

For Example 1, the pointer positions and areas are:

| Left index | Right index | Shorter height | Width | Area | Best area |
| --- | --- | --- | --- | --- | --- |
| 0 | 8 | 1 | 8 | 8 | 8 |
| 1 | 8 | 7 | 7 | 49 | 49 |
| 1 | 7 | 3 | 6 | 18 | 49 |
| 1 | 6 | 8 | 5 | 40 | 49 |
| 2 | 6 | 6 | 4 | 24 | 49 |
| 3 | 6 | 2 | 3 | 6 | 49 |
| 4 | 6 | 5 | 2 | 10 | 49 |
| 5 | 6 | 4 | 1 | 4 | 49 |

The pointers then meet, and the answer is `49`.

### Why This Works

Suppose `height[left] <= height[right]`. The current pair has area `(right - left) * height[left]`. Any pair keeping this left endpoint but choosing an endpoint between `left` and `right` has a smaller width and a water height no greater than `height[left]`. Its area therefore cannot exceed the area just recorded. Discarding the left endpoint cannot lose a better answer. The same argument applies to the right endpoint when it is shorter.

At each step, the best area recorded covers every pair discarded so far, while all pairs that might improve it remain within the current pointer interval. Each movement safely removes one endpoint. When the pointers meet, no candidate pairs remain, so `maxArea` is the global maximum.

Two-element arrays are evaluated exactly once. All-zero heights return `0`, and equal heights, zeros between positive endpoints, and increasing or decreasing heights require no special handling. The largest possible area is `99,999 * 10,000 = 999,990,000`, which fits safely in a Java `int` and is represented exactly by Python integers.

## Time and Space Complexity

Let `n` be the number of lines in `height`. Both implementations have the same bounds.

- **Time: O(n)** — Each iteration moves one pointer inward, giving exactly `n - 1` iterations.
- **Auxiliary space: O(1)** — Only the two indices, the current area, and the maximum area are stored. No additional arrays or slices are created.

## Java 21 Solution

```java
import java.lang.Math;

class Solution {
    public int maxArea(int[] height) {
        int maxArea = 0;
        int left = 0, right = height.length - 1;

        while (left < right) {
            int area = (right - left) * Math.min(height[left], height[right]);
            maxArea = Math.max(maxArea, area);

            if (height[left] <= height[right]) {
                left += 1;
            } else {
                right -= 1;
            }
        }

        return maxArea;
    }
}
```

## Python 3 Solution

```python
from typing import List


class Solution:
    def maxArea(self, height: List[int]) -> int:
        maxArea = 0
        left, right = 0, len(height) - 1

        while left < right:
            area = (right - left) * min(height[left], height[right])
            maxArea = max(maxArea, area)

            if height[left] <= height[right]:
                left += 1
            else:
                right -= 1

        return maxArea
```
