# 34. Find First and Last Position of Element in Sorted Array

![Difficulty: Medium](https://img.shields.io/badge/Difficulty-Medium-orange)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

## Problem Description

Given an array of integers `nums` sorted in non-decreasing order, find the starting and ending position of a given `target` value.

If `target` is not found in the array, return `[-1, -1]`.

You must write an algorithm with `O(log n)` runtime complexity.

## Examples

### Example 1

```text
    Input: nums = [5,7,7,8,8,10], target = 8
    Output: [3,4]
```

### Example 2

```text
    Input: nums = [5,7,7,8,8,10], target = 6
    Output: [-1,-1]
```

### Example 3

```text
    Input: nums = [], target = 0
    Output: [-1,-1]
```


## Constraints

- `0 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- `nums` is a non-decreasing array.
- `-10^9 <= target <= 10^9`

## Solution Approach: Two Boundary Binary Searches

Ordinary binary search stops at any matching element. To find an extreme occurrence, save a match and continue searching toward the desired boundary. Run this search twice: once for the first occurrence and once for the last.

The helper uses inclusive bounds `left` and `right`, a saved `boundary` initialized to `-1`, and a flag `findFirst` (`find_first` in Python) indicating the search direction after a match.

1. Initialize `left = 0`, `right = n - 1`, and `boundary = -1` for each search.
2. While `left <= right`, compute the midpoint:
   - If `nums[mid] < target`, set `left = mid + 1`.
   - If `nums[mid] > target`, set `right = mid - 1`.
   - Otherwise, save `mid` in `boundary`. To find the first occurrence, set `right = mid - 1`; to find the last, set `left = mid + 1`.
3. When the interval becomes empty, return the saved boundary.
4. Return the first-search result and last-search result as a two-element array or list.

Every update excludes the midpoint, ensuring progress even when all elements equal the target. Continuing with binary search after a match preserves logarithmic runtime; walking through adjacent duplicates could require linear time. Compute the midpoint as `left + (right - left) / 2` in Java to avoid adding the bounds directly.

### Walkthrough

For `nums = [5,7,7,8,8,10]` and `target = 8`, each search begins with `[left, right] = [0, 5]`:

| Search | Midpoints visited | Saved boundary and next step |
| --- | --- | --- |
| First occurrence | `2` → `4` → `3` | At `2`, the value is too small. Save matching index `4` and search left; then save matching index `3` and search left again. The interval is empty, so return `3`. |
| Last occurrence | `2` → `4` → `5` | At `2`, the value is too small. Save matching index `4` and search right; index `5` is too large. The interval is empty, so return `4`. |

The combined result is `[3, 4]`.

### Why This Works

For the first-occurrence search, maintain that `boundary` is the smallest matching index seen so far, or `-1` if no match has been seen, and that any possible improvement remains in `[left, right]`.

If the midpoint value is smaller than the target, sorted order rules out the midpoint and everything to its left. If it is larger, sorted order rules out the midpoint and everything to its right. On a match, save its index and search only to its left, since only a smaller index could improve the answer. These updates preserve the invariant. Once the interval is empty, the saved index is the first occurrence, or `-1` if the target is absent.

The last-occurrence search uses the symmetric invariant: save the largest matching index seen and search right after a match. It therefore returns the last occurrence. Each search strictly shrinks its interval, so both terminate.

For an empty array, both loops are skipped and return `-1`. A single matching element produces identical boundaries. The same logic handles duplicates spanning the entire array, matches at either endpoint, and targets outside the array's value range.

## Time and Space Complexity

Let `n` denote the number of elements in `nums`. Both implementations have the same bounds.

- **Time: O(log n)** — Each of the two searches halves its candidate interval per iteration. An empty array takes `O(1)` time.
- **Auxiliary space: O(1)** — Each iterative search stores only a fixed number of variables, without recursion or slicing. The two-element result also uses `O(1)` space.

## Java 21 Solution

```java
class Solution {
    public int intervalBound(int[] nums, int low, int high, int target, boolean upper) {
        if (low < high) {
            int mid = (int) (low + high)/2;

            if ((nums[mid] < target) || (upper && nums[mid] == target)) {
                return this.intervalBound(nums, mid+1, high, target, upper);
            } else {
                return this.intervalBound(nums, low, mid, target, upper);
            }
        }

        return low;
    }

    public int[] searchRange(int[] nums, int target) {
        int lower = this.intervalBound(nums, 0, nums.length, target, false);

        if (lower == nums.length || nums[lower] != target)
            return new int[]{-1, -1};

        int upper = this.intervalBound(nums, 0, nums.length, target, true) - 1;

        return new int[]{lower, upper};
    }
}
```

## Python 3 Solution

```python
from typing import List


class Solution:
    def _find_boundary(self, nums: List[int], target: int, find_first: bool) -> int:
        boundary = -1
        left, right = 0, len(nums)-1

        while left <= right:
            mid = (left + right) // 2

            if nums[mid] < target:
                left = mid + 1
            elif nums[mid] > target:
                right = mid - 1
            else:
                boundary = mid
                
                if find_first:
                    right = mid - 1
                else:
                    left = mid + 1

        return boundary
    
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        first = self._find_boundary(nums, target, True)
        last = self._find_boundary(nums, target, False)
        return [first, last]
```
