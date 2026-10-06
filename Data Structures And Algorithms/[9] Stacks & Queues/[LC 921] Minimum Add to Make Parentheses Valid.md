# 921. Minimum Add to Make Parentheses Valid

![Difficulty: Medium](https://img.shields.io/badge/Difficulty-Medium-orange)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/minimum-add-to-make-parentheses-valid/)

## Problem Description

A parentheses string is valid if and only if:

- It is the empty string,
- It can be written as `AB` (`A` concatenated with `B`), where `A` and `B` are valid strings, or
- It can be written as `(A)`, where `A` is a valid string.

You are given a parentheses string `s`. In one move, you can insert a parenthesis at any position of the string.

For example, if `s = "()))"`, you can insert an opening parenthesis to be `"(()))"` or a closing parenthesis to be `"())))"`.

**Return the minimum number of moves required to make `s` valid**.

## Examples

### Example 1

```text
    Input: s = "())"
    Output: 1
```

**Explanation:** Insert an opening parenthesis before the final closing parenthesis to obtain `"()()"`. One insertion is sufficient and necessary.

### Example 2

```text
    Input: s = "((("
    Output: 3
```

**Explanation:** Append three closing parentheses to obtain `"((()))"`. Each original opening parenthesis needs its own closing parenthesis.

## Constraints

- `1 <= s.length <= 1000`
- `s[i]` is either `'('` or `')'`.

## Solution Approach: Greedy Counting of Unmatched Parentheses

Scan from left to right and match each closing parenthesis with an available opening parenthesis. Only the number of unmatched openings matters because they are all the same character.

Maintain two counters:

- `unmatchedOpenings`: opening parentheses already seen that still need a closing parenthesis.
- `requiredOpenings`: opening parentheses that must be inserted to match closing parentheses encountered without an available opening.

1. Initialize both counters to zero.
2. When the current character is `'('`, increment `unmatchedOpenings`.
3. When it is `')'`, decrement `unmatchedOpenings` if an opening is available. Otherwise, increment `requiredOpenings`: insert an opening immediately before this closing parenthesis. That inserted pair is already complete, so `unmatchedOpenings` stays zero.
4. After the scan, each remaining unmatched opening needs one inserted closing parenthesis. Return `requiredOpenings + unmatchedOpenings`.

Do not allow `unmatchedOpenings` to become negative. Counting only the difference between the total numbers of opening and closing parentheses is insufficient: `")("` has equal totals but requires two insertions because their order is invalid.

### Walkthrough

For Example 1, `s = "())"`:

| Index | Character | Action | Unmatched openings | Required openings |
| --- | --- | --- | --- | --- |
| 0 | `(` | Save an opening for a later match. | 1 | 0 |
| 1 | `)` | Match the available opening. | 0 | 0 |
| 2 | `)` | No opening is available, so one must be inserted. | 0 | 1 |

No openings remain unmatched, so the answer is `1 + 0 = 1`. Inserting `'('` before the last character produces `"()()"`.

### Why This Works

When an opening is available, matching it with the current closing parenthesis uses existing characters without any insertion. Since all openings are identical, choosing an available one cannot reduce the number of future matches.

When no opening is available, the current closing parenthesis requires a new opening somewhere before it. An opening that appears later in the original string cannot match it. Inserting an opening immediately before this character makes the necessary repair with exactly one move and leaves no unmatched opening from that repair.

After all characters have been processed, each remaining unmatched opening requires a distinct closing parenthesis. Appending that many closing parentheses completes the string. The scan makes only necessary repairs and constructs a valid string using exactly `requiredOpenings + unmatchedOpenings` insertions, so this count is minimal.

An already valid string requires zero insertions. A single parenthesis requires one, and a string containing only openings or only closings requires one insertion per character. Mixed strings, including those with closing parentheses before openings, follow the same rules.

## Time and Space Complexity

Let `n` be the length of `s`. Both implementations have the same bounds.

- **Time: O(n)** — Each character is examined once with constant work.
- **Auxiliary space: O(1)** — Only two counters and a fixed number of scalar variables are stored. The Java solution uses `charAt` rather than allocating a character array.

## Java 21 Solution

```java
class Solution {
    public int minAddToMakeValid(String s) {
        int unmatchedOpenings = 0;
        int requiredOpenings = 0;

        for (int index = 0; index < s.length(); index++) {
            char parenthesis = s.charAt(index);

            if (parenthesis == '(') {
                unmatchedOpenings++;
            } else if (unmatchedOpenings > 0) {
                unmatchedOpenings--;
            } else {
                requiredOpenings++;
            }
        }

        return requiredOpenings + unmatchedOpenings;
    }
}
```

## Python 3 Solution

```python
class Solution:
    def minAddToMakeValid(self, s: str) -> int:
        unmatched_openings = 0
        required_openings = 0

        for parenthesis in s:
            if parenthesis == "(":
                unmatched_openings += 1
            elif unmatched_openings > 0:
                unmatched_openings -= 1
            else:
                required_openings += 1

        return required_openings + unmatched_openings
```
