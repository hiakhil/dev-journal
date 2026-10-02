# 20. Valid Parentheses

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/valid-parentheses/)

## Problem Description

Given a string `s` containing just the characters '`(`', '`)`', '`{`', '`}`', '`[`' and '`]`', determine if the input string is valid.

An input string is valid if:
1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.


## Examples

### Example 1

```text
    Input: s = "()"
    Output: true
```

### Example 2

```text
    Input: s = "()[]{}"
    Output: true
```

### Example 3

```text
    Input: s = "(]"
    Output: false
```

### Example 4

```text
    Input: s = "([])"
    Output: true
```

### Example 5

```text
    Input: s = "([)]"
    Output: false
```

## Constraints

- `1 <= s.length <= 10^4`
- `s` contains only the characters `(`, `)`, `[`, `]`, `{`, and `}`.

## Solution Approach: Stack of Expected Closing Brackets

A stack handles nested brackets because the most recently opened pair must close first. Store the closing bracket expected for each unmatched opener. The top of the stack is the only closing bracket that can legally appear next.

1. Start with an empty stack, `expectedClosings` in Java or `expected_closings` in Python.
2. Scan `s` from left to right:
   - For `(`, `[`, or `{`, push its corresponding closer: `)`, `]`, or `}`.
   - For a closing bracket, return `false` if the stack is empty or its top differs from the current bracket. Otherwise, pop that expected closer.
3. After the scan, return whether the stack is empty. Any remaining entry represents an opening bracket that was never closed.

Check that the stack is nonempty before popping. Both implementations use short-circuit evaluation to make this safe. The input guarantee means every character that is not an opener is a closer.

### Walkthrough

For `s = "([])"`, the stack evolves as follows. Entries are shown from bottom to top, so the rightmost entry is the next expected closer.

| Character | Action | Stack afterward |
| --- | --- | --- |
| `(` | Push `)` | `[')']` |
| `[` | Push `]` | `[')', ']']` |
| `]` | Match and pop `]` | `[')']` |
| `)` | Match and pop `)` | `[]` |

The stack ends empty, so the result is `true`. For `"([)]"`, the third character is `)`, but the stack expects `]`, so the algorithm returns `false` immediately.

### Why This Works

After each successfully processed prefix, the stack contains exactly the closers required by its unmatched opening brackets, with the innermost pair's closer on top.

An opener adds a new innermost pair, so pushing its closer preserves this invariant. A closer must match the stack's top to finish that pair. An empty stack means there is no opener to match; a different top means the type or nesting order is wrong. Either failure already makes the prefix invalid, and later characters cannot repair it. A successful match removes precisely the completed pair.

At the end, an empty stack means every opening bracket was matched correctly. A nonempty stack means at least one pair is incomplete. Therefore, the algorithm accepts exactly the valid sequences.

This also covers single-character strings, an initial or extra closer, leftover openers, adjacent pairs, and deeply nested pairs.

## Time and Space Complexity

Let `n` be the length of `s`. Both implementations have the same bounds.

- **Time: O(n)** — Each character is visited once and causes at most one stack push or pop. Stack operations take amortized O(1) time; the Python mapping has only three entries.
- **Auxiliary space: O(n)** — The stack can hold `n` expected closers when every character is an opener. Python's fixed mapping uses O(1) additional space.

## Java 21 Solution

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public boolean isValid(String s) {
        /* Quick check: odd length strings can't be valid */
        if (s.length() % 2 != 0) {
            return false;
        }

        Deque<Character> expectedClosings = new ArrayDeque<>();

        for (char c : s.toCharArray()) {
            if (c == '(' || c == '{' || c == '[') {
                expectedClosings.push(c);
            } else {
                if (expectedClosings.isEmpty()) {
                    return false;
                }

                char top = expectedClosings.pop();

                if (
                    (c == ')' && top != '(') ||
                    (c == '}' && top != '{') ||
                    (c == ']' && top != '[')
                ) {
                    return false;
                }
            }
        }

        return expectedClosings.isEmpty();
    }
}
```

## Python 3 Solution

```python
class Solution:
    def isValid(self, s: str) -> bool:
        expected_closings = []
        closing_for_opening = {"(": ")", "[": "]", "{": "}"}

        for bracket in s:
            if bracket in closing_for_opening:
                expected_closings.append(closing_for_opening[bracket])
            elif not expected_closings or expected_closings.pop() != bracket:
                return False

        return not expected_closings
```
