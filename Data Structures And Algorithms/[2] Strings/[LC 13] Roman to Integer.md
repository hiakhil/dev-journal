# 13. Roman to Integer

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/roman-to-integer/)

## Problem Description

Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`, `C`, `D` and `M`.

Convert a valid Roman numeral string `s` to its integer value. Roman numerals use these symbols:

| Symbol | Value |
| --- | --- |
| `I` | 1 |
| `V` | 5 |
| `X` | 10 |
| `L` | 50 |
| `C` | 100 |
| `D` | 500 |
| `M` | 1000 |

For example, `2` is written as `II` in Roman numeral, just two ones added together. `12` is written as `XII`, which is simply `X + II`. The number `27` is written as `XXVII`, which is `XX + V + II`.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not `IIII`. Instead, the number four is written as `IV`. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as `IX`. There are six instances where subtraction is used:

- `I` can be placed before `V` (5) and `X` (10) to make 4 and 9.
- `X` can be placed before `L` (50) and `C` (100) to make 40 and 90.
- `C` can be placed before `D` (500) and `M` (1000) to make 400 and 900.


## Examples

### Example 1

```text
    Input: s = "III"
    Output: 3
```

**Explanation:** Three `I` symbols contribute `1 + 1 + 1 = 3`.

### Example 2

```text
    Input: s = "LVIII"
    Output: 58
```

**Explanation:** Add `50 + 5 + 1 + 1 + 1 = 58`.

### Example 3

```text
    Input: s = "MCMXCIV"
    Output: 1994
```

**Explanation:** Grouping the numeral gives `M + CM + XC + IV = 1000 + 900 + 90 + 4 = 1994`.

## Constraints

- `1 <= s.length <= 15`
- `s` contains only `I`, `V`, `X`, `L`, `C`, `D`, and `M`.
- It is **guaranteed** that `s` is a valid roman numeral in the range `[1, 3999]`.


## Solution Approach: Left-to-Right Scan with Lookahead

For each symbol, compare its value with the next symbol's value. If the current value is smaller, it starts a subtractive pair and contributes negatively. Otherwise, it contributes positively.

1. Initialize `total` to `0` and use a fixed mapping for the seven symbol values.
2. Visit every character in `s`. Subtract its value from `total` if a next character exists and has a larger value; otherwise, add its value.
3. Return `total` after the scan.

Use a strict comparison: equal values are added, as in `III`. Check the next index before accessing it; the final symbol is always added. Each symbol is processed once, including both symbols of a subtractive pair. For example, `IV` contributes `-1 + 5`, so no index needs to be skipped.

The validity guarantee lets the algorithm use this local comparison without separately checking numeral syntax. The Java helper maps a character to its value with a `switch`; Python uses a dictionary with seven entries.

### Walkthrough

For `s = "MCMXCIV"`:

| Current symbol | Current value | Next value | Contribution | Running total |
| --- | --- | --- | --- | --- |
| `M` | 1000 | 100 | +1000 | 1000 |
| `C` | 100 | 1000 | -100 | 900 |
| `M` | 1000 | 10 | +1000 | 1900 |
| `X` | 10 | 100 | -10 | 1890 |
| `C` | 100 | 1 | +100 | 1990 |
| `I` | 1 | 5 | -1 | 1989 |
| `V` | 5 | None | +5 | 1994 |

The final total is `1994`.

### Why This Works

In a valid numeral, a symbol is followed by a larger value exactly when it is the first symbol of a subtractive pair. Assign that symbol a negative contribution and all other symbols a positive contribution.

After each iteration, `total` equals the sum of these signed contributions for all processed symbols. The comparison selects the correct sign for the current symbol, so every iteration preserves this invariant.

A subtractive pair with values `a < b` contributes `-a + b = b - a`, and each ordinary symbol contributes its own value. Once every symbol has been processed, these contributions sum to the numeral's integer value, so the returned result is correct.

Single-symbol inputs are added directly, repeated symbols are added rather than subtracted, and all six subtractive pairs use the same comparison. Values up to `3999` and strings of length `15` fit comfortably within Java's `int`; no large intermediate values occur.

## Time and Space Complexity

Let `n` be the length of `s`. Both implementations have the same bounds.

- **Time: O(n)** — Each symbol is visited once with at most two constant-time value lookups. The Python dictionary has a fixed size of seven entries.
- **Auxiliary space: O(1)** — Only a few variables and a fixed-size symbol mapping are needed. Neither implementation copies or slices the input.

## Java 21 Solution

```java
class Solution {
    public int romanToInt(String s) {
        int total = 0;

        for (int index = 0; index < s.length(); index++) {
            int currentValue = symbolValue(s.charAt(index));

            if (index + 1 < s.length() && currentValue < symbolValue(s.charAt(index + 1))) {
                total -= currentValue;
            } else {
                total += currentValue;
            }
        }

        return total;
    }

    private int symbolValue(char symbol) {
        return switch (symbol) {
            case 'I' -> 1;
            case 'V' -> 5;
            case 'X' -> 10;
            case 'L' -> 50;
            case 'C' -> 100;
            case 'D' -> 500;
            case 'M' -> 1000;
            default -> throw new IllegalArgumentException("Unknown Roman symbol");
        };
    }
}
```

## Python 3 Solution

```python
class Solution:
    def romanToInt(self, s: str) -> int:
        total = 0
        symbol_values = {
            "I": 1,
            "V": 5,
            "X": 10,
            "L": 50,
            "C": 100,
            "D": 500,
            "M": 1000,
        }

        for index, symbol in enumerate(s):
            current_value = symbol_values[symbol]

            if index + 1 < len(s) and current_value < symbol_values[s[index + 1]]:
                total -= current_value
            else:
                total += current_value

        return total
```
