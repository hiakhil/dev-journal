# 3. Longest Substring Without Repeating Characters

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)

## Problem Description

For an input string `s`, return the maximum length of a contiguous segment in which every character is distinct.

A **substring** contains consecutive characters from the original string. Characters cannot be skipped, as they can in a subsequence. Return the length, rather than the substring itself.

## Examples

The following inputs and outputs are from the linked LeetCode statement; explanations are paraphrased.

### Example 1

```text
Input: s = "abcabcbb"
Output: 3
```

**Explanation:** `"abc"`, `"bca"`, and `"cab"` each contain three distinct consecutive characters. No longer segment meets the requirement.

### Example 2

```text
Input: s = "bbbbb"
Output: 1
```

**Explanation:** Any segment containing two or more characters repeats `b`, so a single `"b"` is optimal.

### Example 3

```text
Input: s = "pwwkew"
Output: 3
```

**Explanation:** `"wke"` is a valid segment of length three. Although `"pwke"` has distinct characters, it requires skipping a character and is therefore a subsequence, not a substring.

## Constraints

- The length of `s` is between `0` and `100,000`, inclusive.
- Allowed characters include English letters, numerical digits, symbols, and spaces.

## Solution Approach: Sliding Window with Last-Seen Indices

Maintain a window from `left` to `right` whose characters are all distinct. A hash map stores the most recent index of each character encountered.

For each position `right`:

1. Read the current character and retrieve its previous index, if present.
2. If that index lies within the current window, move `left` to one position after it. This excludes the earlier occurrence and removes the duplicate.
3. Record the current index as the character's most recent occurrence.
4. Update the maximum length using `right - left + 1`.

Use `Math.max(left, previousIndex + 1)` in Java or `max(left, previous_index + 1)` in Python when updating the boundary. This prevents `left` from moving backward when the previous occurrence is already outside the window. For example, in `"abba"`, the final `a` must not move `left` back to index `1` after it has reached index `2`.

### Walkthrough

For `s = "pwwkew"`:

| `right` | Character | `left` after adjustment | Valid window | Maximum length |
| :---: | :---: | :---: | :--- | :---: |
| 0 | `p` | 0 | `p` | 1 |
| 1 | `w` | 0 | `pw` | 2 |
| 2 | `w` | 2 | `w` | 2 |
| 3 | `k` | 2 | `wk` | 2 |
| 4 | `e` | 2 | `wke` | 3 |
| 5 | `w` | 3 | `kew` | 3 |

### Why This Works

Before adding a character, the existing window has no duplicates. Adding the next character can introduce only a duplicate of that character. Moving `left` past its previous occurrence restores the invariant while retaining the longest valid window ending at `right`.

Every possible ending position is considered, so the largest window length encountered is the answer. For an empty string, the loop does not run and the result remains `0`.

## Time and Space Complexity

Let `n` be the string length and `k` the number of possible distinct characters in the input alphabet.

These bounds apply to both the Java 21 and Python 3 implementations below.

- **Time: O(n)** on average, assuming constant-time hash map operations. Each character is processed once, and the left boundary never moves backward.
- **Auxiliary space: O(min(n, k))** because the map stores one index per distinct character encountered. For a fixed-size alphabet, this is bounded by **O(1)** relative to `n`.

## Java 21 Solution

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> lastSeen = new HashMap<>();
        int left = 0, maxLength = 0;

        for (int right = 0; right < s.length(); right++) {
            Integer previousIndex = lastSeen.get(s.charAt(right));

            if (previousIndex != null) {
                left = Math.max(left, previousIndex + 1);
            }

            lastSeen.put(s.charAt(right), right);
            maxLength = Math.max(maxLength, right - left + 1);
        }

        return maxLength;
    }
}
```

## Python 3 Solution

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        last_seen, left, max_length = {}, 0, 0
        
        for right, current in enumerate(s):
            previous_index = last_seen.get(current)

            if previous_index is not None:
                left = max(left, previous_index + 1)

            last_seen[current] = right
            max_length = max(max_length, right - left + 1)

        return max_length
```
