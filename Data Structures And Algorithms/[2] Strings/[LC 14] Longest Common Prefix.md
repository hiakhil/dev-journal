# 14. Longest Common Prefix

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/longest-common-prefix/)

## Problem Description

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string "".

## Examples

### Example 1

```text
    Input: strs = ["flower","flow","flight"]
    Output: "fl"
```

### Example 2

```text
    Input: strs = ["dog","racecar","car"]
    Output: ""
```

## Constraints

- `1 <= strs.length <= 200`
- `0 <= strs[i].length <= 200`
- `strs[i]` consists of only lowercase English letters if it is non-empty.

## Solution Approach: Vertical Scanning

Compare the strings one character position at a time. A position belongs to the common prefix only if every string has the same character there. The prefix cannot be longer than the shortest input string.

1. Store the first string as `first` and find the shortest string length.
2. For each index below that length, compare `first[index]` with the character at the same index in every other string.
3. At the first mismatch, return the part of `first` before that index.
4. If all checked positions match, return the prefix of `first` whose length equals the shortest string length.

The array is guaranteed to be nonempty, so reading its first element is safe. Limiting the scan to the shortest length keeps every character access within bounds, including when an input string is empty. The implementations create the result only when returning; they do not repeatedly build shorter candidate prefixes.

### Walkthrough

For `strs = ["flower", "flow", "flight"]`, the shortest length is `4`.

| Index | `flower` | `flow` | `flight` | Decision |
| --- | --- | --- | --- | --- |
| 0 | `f` | `f` | `f` | All match; continue. |
| 1 | `l` | `l` | `l` | All match; continue. |
| 2 | `o` | `o` | `i` | Mismatch; stop. |

Return the characters before index `2`: `"fl"`.

### Why This Works

Before checking an index, every earlier position has been verified to match across all input strings. This is initially true because no positions have been checked. When every string matches the current character, extending the verified prefix by one character preserves this invariant.

If a mismatch occurs at index `i`, the first `i` characters form a common prefix, but any longer prefix would include the mismatching position. Returning those `i` characters therefore gives the longest possible common prefix.

If the scan reaches the shortest string's length, every character of that string has matched. No common prefix can extend beyond the end of an input string, so the returned prefix is again longest.

An empty input string makes the shortest length zero and produces `""`. A single input string is returned in full. Identical strings, a shorter string that is a prefix of all the others, and an immediate mismatch follow the same rules.

## Time and Space Complexity

Let `n` be the number of strings, `m` the length of the shortest string, and `p` the length of the returned prefix, where `p <= m`. Both implementations have the same bounds.

- **Time: O(n × (m + 1))** — Finding the shortest length takes O(n), and scanning at most `m` positions takes O(n × m). Creating the returned prefix takes at most O(p). The bound includes the O(n) length scan when `m = 0`; for nonempty strings, it simplifies to O(n × m).
- **Auxiliary space: O(1), excluding the result** — The scan uses a fixed number of variables. Python finds the shortest length with a generator and indexes the original list, so it creates no temporary list. Java's `substring` and Python's string slice can allocate O(p) space for the returned prefix.

## Java 21 Solution

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        String first = strs[0];
        int shortestLength = first.length();

        for (String word : strs) {
            shortestLength = Math.min(shortestLength, word.length());
        }

        for (int index = 0; index < shortestLength; index++) {
            char expectedCharacter = first.charAt(index);

            for (int stringIndex = 1; stringIndex < strs.length; stringIndex++) {
                if (strs[stringIndex].charAt(index) != expectedCharacter) {
                    return first.substring(0, index);
                }
            }
        }

        return first.substring(0, shortestLength);
    }
}
```

## Python 3 Solution

```python
from typing import List


class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        first = strs[0]
        shortest_length = min(len(word) for word in strs)

        for index in range(shortest_length):
            expected_character = first[index]

            for string_index in range(1, len(strs)):
                if strs[string_index][index] != expected_character:
                    return first[:index]

        return first[:shortest_length]
```
