# 88. Merge Sorted Array

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/merge-sorted-array/)

## Problem Description

Combine two sorted integer arrays into `nums1`, keeping the result in non-decreasing order. Only the first `m` entries of `nums1` belong to the input; its remaining `n` slots are zero-filled capacity for the merge. All `n` entries of `nums2` belong to the input.

Modify `nums1` directly without returning a result. A zero within its first `m` entries is a real value, not an unused slot.

**Follow-up:** Achieve `O(m + n)` running time.

## Examples

The following inputs and outputs are from the linked LeetCode statement; explanations are paraphrased.

### Example 1

```text
Input: nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: [1,2,2,3,5,6]
```

**Explanation:** Combining the valid entries preserves both copies of `2` and places every value in sorted order.

### Example 2

```text
Input: nums1 = [1], m = 1, nums2 = [], n = 0
Output: [1]
```

**Explanation:** The second array contributes nothing, so the first stays unchanged.

### Example 3

```text
Input: nums1 = [0], m = 0, nums2 = [1], n = 1
Output: [1]
```

**Explanation:** The first array has no valid input entries. Copy `1` into its reserved slot.

## Constraints

- `nums1.length == m + n`
- `nums2.length == n`
- `0 <= m, n <= 200`
- `1 <= m + n <= 200`
- `-10^9 <= nums1[i], nums2[j] <= 10^9`
- The first `m` entries of `nums1` and all entries of `nums2` are sorted in non-decreasing order.

## Solution Approach: Merge from the End

The largest remaining value must be at the end of one of the two sorted inputs. Fill the destination from right to left so that moving a value never overwrites an unread entry in `nums1`.

1. Set `first = m - 1` and `second = n - 1` to the last valid input entries. Set `write = m + n - 1` to the final destination slot.
2. While `nums2` still has unread entries, compare the two remaining end values. Place the larger at `nums1[write]` and move its input pointer left. If `nums1` is exhausted, take from `nums2`.
3. Move `write` left after each placement. Once `nums2` is exhausted, any remaining entries of `nums1` are already correctly positioned.

Check `first >= 0` before reading `nums1[first]`. Equal values can be taken from either input; this implementation takes from `nums2` on ties. Java requires no imports; Python imports `List` for type annotations.

### Walkthrough

For Example 1, begin with `first = 2`, `second = 2`, and `write = 5`.

| Destination index | Values compared | Value placed |
| --- | --- | --- |
| 5 | `3` and `6` | `6` from `nums2` |
| 4 | `3` and `5` | `5` from `nums2` |
| 3 | `3` and `2` | `3` from `nums1` |
| 2 | `2` and `2` | `2` from `nums2` |

Now `nums2` is exhausted. The leading `[1,2]` is already correct, giving `[1,2,2,3,5,6]`.

### Why This Works

Before each iteration, the suffix after `write` contains the largest processed values in their final sorted positions. Since each remaining input is sorted, its last unread value is its largest. Selecting the larger of these values therefore fills the next destination slot correctly and preserves the invariant.

While `second >= 0`, the pointer relation `write = first + second + 1` guarantees `write > first`. Writes cannot destroy unread values in `nums1`. Each iteration consumes one input value, so the loop terminates. When `nums2` is exhausted, `write == first`, and the remaining prefix of `nums1` already occupies its final positions.

If `m == 0`, every value is copied from `nums2`. If `n == 0`, the loop does nothing. Duplicates, negative numbers, and valid zeros require no special handling. Values are compared and copied without arithmetic, avoiding overflow even at the constraint limits.

## Time and Space Complexity

Let `m` and `n` be the numbers of valid input entries in `nums1` and `nums2`, respectively.

Both implementations have the following bounds.

- **Time: O(m + n)** — Each iteration places one value; at most `m + n` values are processed.
- **Auxiliary space: O(1)** — Only three indices are stored. The result uses the existing capacity of `nums1`.

## Java 21 Solution

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int first = m-1;
        int second = n-1;
        int write = m+n-1;

        while (second >= 0) {
            if (first >= 0 && nums1[first] > nums2[second]) {
                nums1[write] = nums1[first];
                first -= 1;
            } else {
                nums1[write] = nums2[second];
                second -= 1;
            }

            write -= 1;
        }
    }
}
```

## Python 3 Solution

```python
from typing import List


class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        first, second, write = m-1, n-1, m+n-1

        while second >= 0:
            if first >= 0 and nums1[first] > nums2[second]:
                nums1[write] = nums1[first]
                first -= 1
            else:
                nums1[write] = nums2[second]
                second -= 1
            write -= 1
```
