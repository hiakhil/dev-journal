# 121. Best Time to Buy and Sell Stock

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

## Problem Description

The array `prices` records a stock's price on successive days. Find the greatest profit available from buying once and selling on a strictly later day.

Profit is the selling price minus the buying price. Return `0` if no profitable transaction exists. You may make at most one transaction; you cannot sell before buying or buy and sell on the same day.

## Examples

The following inputs and outputs are from the linked LeetCode statement; explanations are paraphrased.

### Example 1

```text
Input: prices = [7,1,5,3,6,4]
Output: 5
```

**Explanation:** Using one-based day numbers, buying on day 2 for `1` and selling on day 5 for `6` yields `5`. The earlier price of `7` cannot serve as the selling price for a purchase on day 2.

### Example 2

```text
Input: prices = [7,6,4,3,1]
Output: 0
```

**Explanation:** Every later price is lower, so skipping the transaction gives the best result, `0`.

## Constraints

- `1 <= prices.length <= 10^5`
- `0 <= prices[i] <= 10^4`

## Solution Approach: Track the Lowest Earlier Price

For a fixed selling day, the best purchase is the cheapest price on any earlier day. Scan from left to right, retaining that minimum in `minPrice` and the largest profit found in `maxProfit`.

1. Initialize `minPrice` to the first price and `maxProfit` to `0`.
2. Starting at the second day, compute the profit from selling today at `prices[day]` after buying at `minPrice`. Update `maxProfit` if this is better.
3. Update `minPrice` with today's price so it can be used for future selling days.
4. Return `maxProfit` after the scan.

Compute today's profit before updating the minimum. This keeps the candidate purchase strictly earlier than the sale. The input is never modified, and the Python implementation uses indices rather than creating a slice.

### Walkthrough

For `prices = [7,1,5,3,6,4]`, initialize `minPrice = 7` and `maxProfit = 0`.

| Selling day (one-based) | Today's price | Lowest earlier price | Candidate profit | Best profit so far |
| --- | --- | --- | --- | --- |
| 2 | 1 | 7 | -6 | 0 |
| 3 | 5 | 1 | 4 | 4 |
| 4 | 3 | 1 | 2 | 4 |
| 5 | 6 | 1 | 5 | 5 |
| 6 | 4 | 1 | 3 | 5 |

The answer is `5`, obtained by buying at `1` and selling later at `6`.

### Why This Works

Before processing each selling day, `minPrice` is the minimum price among all earlier days. Subtracting it from today's price therefore gives the best possible profit for a transaction ending today. Updating the minimum afterward preserves this property for the next day.

Meanwhile, `maxProfit` stores the best profit among all selling days already considered, or `0` if none is profitable. Every possible selling day after the first is examined, so the final value is the best valid profit overall.

With only one price, the loop does not run and returns `0`. Decreasing or equal prices also return `0`; rising prices, repeated minima, and zero prices need no special handling. Price differences lie between `-10,000` and `10,000`, so Java `int` arithmetic is safe.

## Time and Space Complexity

Let `n` be the number of entries in `prices`. Both implementations have the same bounds.

- **Time: O(n)** — Each day is processed once with constant work.
- **Auxiliary space: O(1)** — Only the minimum price, best profit, and loop index are stored; no additional arrays or slices are created.

## Java 21 Solution

```java
class Solution {
    public int maxProfit(int[] prices) {
        int minPrice = prices[0];
        int maxProfit = 0;

        for (int day = 1; day < prices.length; day++) {
            maxProfit = Math.max(maxProfit, prices[day] - minPrice);
            minPrice = Math.min(minPrice, prices[day]);
        }

        return maxProfit;
    }
}
```

## Python 3 Solution

```python
from typing import List


class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        minPrice = prices[0]
        maxProfit = 0

        for day in range(1, len(prices)):
            maxProfit = max(maxProfit, prices[day] - minPrice)
            minPrice = min(minPrice, prices[day])

        return maxProfit
```
