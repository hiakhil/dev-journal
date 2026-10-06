# 706. Design HashMap

![Difficulty: Easy](https://img.shields.io/badge/Difficulty-Easy-green)

**Source:** [LeetCode problem statement](https://leetcode.com/problems/design-hashmap/)

## Problem Description

Design a HashMap without using any built-in hash table libraries.

Implement the `MyHashMap` class:

- `MyHashMap()` initializes the object with an empty map.
- `void put(int key, int value)` inserts a `(key, value)` pair into the HashMap. If the `key` already exists in the map, update the corresponding `value`.
- `int get(int key)` returns the `value` to which the specified `key` is mapped, or `-1` if this map contains no mapping for the `key`.
- `void remove(key)` removes the `key` and its corresponding `value` if the map contains the mapping for the `key`.

## Examples

Consider the following inputs and outputs; explanations are paraphrased.

### Example 1

```text
    Input:
        ["MyHashMap", "put", "put", "get", "get", "put", "get", "remove", "get"]
        [[], [1, 1], [2, 2], [1], [3], [2, 1], [2], [2], [2]]

    Output:
        [null, null, null, 1, -1, null, 1, null, -1]

    Explanation:
        MyHashMap myHashMap = new MyHashMap();
        myHashMap.put(1, 1); // The map is now [[1,1]]
        myHashMap.put(2, 2); // The map is now [[1,1], [2,2]]
        myHashMap.get(1);    // return 1, The map is now [[1,1], [2,2]]
        myHashMap.get(3);    // return -1 (i.e., not found)
        myHashMap.put(2, 1); // The map is now [[1,1], [2,1]]
        myHashMap.get(2);    // return 1, The map is now [[1,1], [2,1]]
        myHashMap.remove(2); // remove the mapping for 2, The map is now [[1,1]]
        myHashMap.get(2);    // return -1 (i.e., not found), The map is now [[1,1]]
```

## Constraints

- `0 <= key, value <= 10^6`
- At most `10^4` calls will be made to `put`, `get`, and `remove` combined.

## Solution Approach: Direct-Address Table

Every valid key lies between `0` and `1,000,000`, inclusive. Allocate an array with `1,000,001` entries and use each key directly as an index. The entry `values[key]` holds that key's value, or `-1` when the key is absent. Arrays and lists satisfy the restriction against built-in hash table libraries.

1. **Initialize:** Fill every entry with `-1`, so the map starts empty.
2. **Insert or update:** Assign `values[key] = value`. Writing to the same index replaces an existing mapping.
3. **Look up:** Return `values[key]` directly.
4. **Remove:** Assign `values[key] = -1` to mark the key as absent.

The sentinel `-1` is safe because valid values are nonnegative; `0` remains a valid stored value. The array needs one more entry than the maximum key to include index `1,000,000`. This approach reserves storage for the entire key range, regardless of how many keys are present.

### Walkthrough

Only entries affected by Example 1 are shown below. All other entries remain `-1`.

| Operation | `values[1]` | `values[2]` | Result |
| --- | --- | --- | --- |
| `MyHashMap()` | `-1` | `-1` | `null` |
| `put(1, 1)` | `1` | `-1` | `null` |
| `put(2, 2)` | `1` | `2` | `null` |
| `get(1)` | `1` | `2` | `1` |
| `get(3)` | `1` | `2` | `-1` |
| `put(2, 1)` | `1` | `1` | `null` |
| `get(2)` | `1` | `1` | `1` |
| `remove(2)` | `1` | `-1` | `null` |
| `get(2)` | `1` | `-1` | `-1` |

### Why This Works

Maintain the invariant that, for every valid key, its array entry contains its current mapped value, or `-1` if the key is absent.

Initialization establishes this invariant for an empty map. A `put` operation writes the requested value to exactly that key's entry, correctly handling both insertion and replacement. A `remove` operation restores the absence marker at that entry, including when the key was already absent. Neither operation affects other keys. Therefore, the invariant holds after every operation, and `get` always returns the required value or `-1`.

Keys `0` and `1,000,000` both have valid array indices. Stored values of `0` and `1,000,000`, repeated updates, removal of absent keys, and reinsertion after removal all follow the same invariant.

## Time and Space Complexity

Let `U = 1,000,001` be the number of possible keys and `Q` be the total number of `put`, `get`, and `remove` calls. Both implementations have the same bounds.

- **Time: O(U) for construction; O(1) per operation** — Construction initializes every array entry. Each subsequent operation accesses one entry, giving `O(U + Q)` time for construction followed by `Q` calls. These are worst-case bounds.
- **Auxiliary space: O(U)** — The map stores one entry for every possible key, independently of the number of keys actually inserted.

## Java 21 Solution

```java
import java.util.Arrays;

class MyHashMap {
    private final int[] map;
    
    public MyHashMap() {
        this.map = new int[1000001];

        /* Initializes all the 1000001 values as -1. */
        Arrays.fill(this.map, -1);
    }
    
    public void put(int key, int value) {
        this.map[key] = value;
    }
    
    public int get(int key) {
        return this.map[key];
    }
    
    public void remove(int key) {
        this.map[key] = -1;
    }
}
```

## Python 3 Solution

```python
class MyHashMap:
    def __init__(self) -> None:
        self.values = [-1]*1000001

    def put(self, key: int, value: int) -> None:
        self.values[key] = value

    def get(self, key: int) -> int:
        return self.values[key]

    def remove(self, key: int) -> None:
        self.values[key] = -1
```
