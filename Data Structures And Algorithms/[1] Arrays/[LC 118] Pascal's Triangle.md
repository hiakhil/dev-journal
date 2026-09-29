# 118. Pascal's Triangle

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/pascals-triangle/description/)

## Problem Description

Given `numRows`, return that many rows of Pascal's triangle as a list of lists, starting at the top.

The first row is `[1]`. Each subsequent row has one more entry than the previous row and begins and ends with `1`. Every interior entry is formed by adding the two adjacent entries above it in the previous row.

## Examples

The following inputs and outputs are from the linked LeetCode statement; explanations are paraphrased.

### Example 1

```text
Input: numRows = 5
Output: [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]
```

**Explanation:** Build five rows in order. For example, the interior values of the fifth row are `1 + 3 = 4`, `3 + 3 = 6`, and `3 + 1 = 4`.

### Example 2

```text
Input: numRows = 1
Output: [[1]]
```

**Explanation:** Only the top row is requested.

## Constraints

- `1 <= numRows <= 30`

## Solution Approach: Build Rows from the Previous Row

Store completed rows in `triangle`. To construct row index `rowIndex` (using zero-based indexing), create a new list containing `rowIndex + 1` entries. Read interior values from the preceding completed row, which is already part of the output.

1. Initialize an empty list called `triangle`.
2. For each `rowIndex` from `0` through `numRows - 1`, create a separate list called `row`.
3. For each column from `0` through `rowIndex`, append `1` at either edge. Otherwise, append `triangle[rowIndex - 1][column - 1] + triangle[rowIndex - 1][column]`.
4. Append the completed row to `triangle`. Return `triangle` after all rows have been built.

The edge condition handles the first row without accessing a nonexistent previous row. Create a new list for every row so that rows do not share mutable storage.

### Walkthrough

For `numRows = 5`:

| Row index | Interior calculations | Completed row |
| --- | --- | --- |
| 0 | None | `[1]` |
| 1 | None | `[1,1]` |
| 2 | `1 + 1` | `[1,2,1]` |
| 3 | `1 + 2`, `2 + 1` | `[1,3,3,1]` |
| 4 | `1 + 3`, `3 + 3`, `3 + 1` | `[1,4,6,4,1]` |

### Why This Works

The first iteration produces `[1]`, the correct top row. Assume all rows before `rowIndex` are correct. The new row receives `1` at both edges, and each interior entry is the sum of the appropriate adjacent entries in the previous row. These are exactly the rules defining Pascal's triangle, so the new row is correct as well.

By induction, every appended row is correct. The outer loop produces exactly `numRows` rows in order, giving the required result. When `numRows == 1`, only the first row is produced. At the maximum input of `30`, the largest entry is `77,558,760`, which fits in a Java `int`; Python integers also represent it exactly.

## Time and Space Complexity

Let `n = numRows`. Both implementations have the same bounds.

- **Time: O(n²)** — The loops generate `1 + 2 + ... + n = n(n + 1) / 2` entries, doing constant work per entry. This is optimal for explicitly returning every entry.
- **Auxiliary space: O(1), excluding the output** — Only loop indices and list references are needed beyond the result. Every newly allocated row becomes part of the returned triangle; no separate previous-row copy is stored.
- **Output space: O(n²)** — The returned lists store all `n(n + 1) / 2` entries, so total space including the output is `O(n²)`.

## Java 21 Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> triangle = new ArrayList<>(numRows);

        for (int rowIndex = 0; rowIndex < numRows; rowIndex++) {
            List<Integer> row = new ArrayList<>(rowIndex + 1);

            for (int column = 0; column <= rowIndex; column++) {
                if (column == 0 || column == rowIndex) {
                    row.add(1);
                } else {
                    List<Integer> previousRow = triangle.get(rowIndex - 1);
                    row.add(previousRow.get(column - 1) + previousRow.get(column));
                }
            }

            triangle.add(row);
        }

        return triangle;
    }
}
```

## Python 3 Solution

```python
from typing import List


class Solution:
    def generate(self, numRows: int) -> List[List[int]]:
        triangle = []

        for rowIndex in range(numRows):
            row = []

            for column in range(rowIndex + 1):
                if column == 0 or column == rowIndex:
                    row.append(1)
                else:
                    previousRow = triangle[rowIndex - 1]
                    row.append(previousRow[column - 1] + previousRow[column])

            triangle.append(row)

        return triangle
```
