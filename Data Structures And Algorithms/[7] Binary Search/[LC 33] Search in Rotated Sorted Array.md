# 33. Search in Rotated Sorted Array

![Difficulty: Medium](https://img.shields.io/badge/Difficulty-Medium-orange)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/search-in-rotated-sorted-array/)

## Problem Description

There is an integer array `nums` sorted in ascending order (with **distinct** values).

Prior to being passed to your function, `nums` is **possibly left rotated** at an unknown index `k` (`1 <= k < nums.length`) such that the resulting array is `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (**0-indexed**). For example, `[0,1,2,4,5,6,7]` might be left rotated by `3` indices and become `[4,5,6,7,0,1,2]`.

Given the array `nums` **after** the possible rotation and an integer `target`, return the index of `target` if it is in `nums`, or `-1` if it is not in `nums`.

You must write an algorithm with `O(log n)` runtime complexity.

## Examples

### Example 1

```text
    Input: nums = [4,5,6,7,0,1,2], target = 0
    Output: 4
```

### Example 2

```text
    Input: nums = [4,5,6,7,0,1,2], target = 3
    Output: -1
```

### Example 3

```text
    Input: nums = [1], target = 0
    Output: -1
```

## Constraints

- `1 <= nums.length <= 5000`
- `-10^4 <= nums[i] <= 10^4`
- All elements in `nums` are **unique**.
- `nums` is an ascending array that is possibly rotated.
- `-10^4 <= target <= 10^4`

## Solution Approach: Modified Binary Search

A rotated ascending array has at most one point where adjacent values decrease. Splitting the current search interval at its midpoint therefore leaves at least one half sorted. Use that half's endpoint values to decide which half can contain the target.

1. Initialize `left = 0` and `right = len(nums) - 1`. These bounds include every remaining candidate index.
2. While `left <= right`, calculate `mid`. If `nums[mid] == target`, return `mid`.
3. If `nums[left] <= nums[mid]`, the left half is sorted:
   - If `nums[left] <= target < nums[mid]`, keep it by setting `right = mid - 1`.
   - Otherwise, set `left = mid + 1`.
4. Otherwise, the right half is sorted:
   - If `nums[mid] < target <= nums[right]`, keep it by setting `left = mid + 1`.
   - Otherwise, set `right = mid - 1`.
5. Return `-1` if the interval becomes empty.

The midpoint is excluded from both updates because it has already been checked. Include equality at the outer endpoints so a target at `left` or `right` is retained. The comparison `nums[left] <= nums[mid]` also handles a left half containing just one element. Distinct values make this sorted-half test unambiguous.

### Walkthrough

For `nums = [4,5,6,7,0,1,2]` and `target = 0`:

| `left` | `right` | `mid` | Decision |
| --- | --- | --- | --- |
| 0 | 6 | 3 | The left half `[4,5,6,7]` is sorted, but its range excludes `0`; set `left = 4`. |
| 4 | 6 | 5 | The left half `[0,1]` is sorted and its range includes `0`; set `right = 4`. |
| 4 | 4 | 4 | `nums[4] == 0`; return `4`. |

### Why This Works

Maintain the invariant that if the target exists and has not been returned, its index lies within `[left, right]`. This holds initially because the interval covers the entire array.

After checking the midpoint, identify a sorted half. If the target falls within that half's endpoint range, it cannot occur in the other half: distinct values in a rotated ascending array place the other half's values outside that range. Otherwise, the sorted half cannot contain it. Each update therefore preserves the invariant while discarding the midpoint and approximately half the candidates.

The interval strictly shrinks, so the search terminates. A returned index contains the target by the equality check; an empty interval proves the target is absent. The same reasoning covers unrotated arrays, single-element arrays, targets at either endpoint or at the rotation boundary, and missing targets.

## Time and Space Complexity

Let `n` denote the number of elements in `nums`. Both implementations have the same bounds.

- **Time: O(log n)** — Each iteration reduces the candidate interval by roughly half and performs constant work.
- **Auxiliary space: O(1)** — Only a fixed number of indices are stored; neither implementation uses recursion, slices, or additional arrays.

## Java 21 Solution

```java
class Solution {
    public int search(int[] nums, int target) {
        int low = 0, high = nums.length - 1;

        while(low <= high) {
            int mid = (int) (low + high) / 2;

            if(nums[mid] == target) {
                return mid;
            }

            // left half is sorted
            if(nums[low] <= nums[mid]) {
                if(nums[low] <= target && target < nums[mid]) {
                    high = mid - 1;
                } else {
                    low = mid + 1;
                }
            } else { 
                // right half is sorted
                if (nums[mid] < target && target <= nums[high]) {
                    low = mid + 1;
                } else {
                    high = mid - 1;
                }
            }
        }

        return -1;
    }
}
```

## Python 3 Solution

```python
from typing import List


class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums) - 1

        while left <= right:
            mid = (left + right) // 2

            if nums[mid] == target:
                return mid

            if nums[left] <= nums[mid]:
                if nums[left] <= target < nums[mid]:
                    right = mid - 1
                else:
                    left = mid + 1
            else:
                if nums[mid] < target <= nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1

        return -1
```
