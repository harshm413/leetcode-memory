## 🪟🔍 _The Nearby Duplicate: The Contains Duplicate II Saga_

> \_"The Oracle was given an array and a number `k`.
>
> She was commanded:
>
> **'Are there two DISTINCT indices i and j
> where nums[i] == nums[j] and |i - j| ≤ k?'**
>
> Not just any duplicate — a duplicate within DISTANCE k.
> Two identical values that are at most k positions apart.
>
> The Oracle used a **sliding window of size k** with a hash set:
>
> **Maintain a set of the last k elements.**
> For each new element:
> -   If it's already in the set → duplicate within k. Return true.
> -   Add it to the set.
> -   If the set grows beyond k → remove the oldest element.
>
> The set is the window. It holds at most k elements.
> If a duplicate exists within that window — found it."\_

---

This is the saga of **Contains Duplicate II (LeetCode 219)**.

Given an array `nums` and integer `k`:
-   Return `true` if there exist indices `i` and `j` where
    `nums[i] == nums[j]` and `|i - j| <= k`.

```
Input:  nums = [1,2,3,1], k = 3
Output: true   (nums[0] == nums[3], distance = 3 ≤ 3)

Input:  nums = [1,0,1,1], k = 1
Output: true   (nums[2] == nums[3], distance = 1 ≤ 1)

Input:  nums = [1,2,3,1,2,3], k = 2
Output: false  (nearest duplicates are distance 3 apart, > k=2)
```

---

## 🧠 The Sliding Window + Hash Set Insight

We maintain a "window" of the last k elements using a hash set.

At each index `i`:
1. **Check:** is `nums[i]` already in the set?
   If yes → a duplicate exists within distance k. Return true.
2. **Add:** insert `nums[i]` into the set.
3. **Evict:** if the set has more than k elements, remove `nums[i - k]`.
   This keeps the window at size ≤ k.

The set always contains elements from indices `[i-k, i]` (at most).
If a duplicate is found in this range — the distance condition is satisfied.

> _"The set is a sliding window of size k.
> If the new element is already inside — it's a nearby duplicate.
> If not — add it and evict the oldest to maintain the window size."_

---

### 📜 The Scroll of the Nearby Duplicate

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;
```

---

## 🔍 The Sliding Window Solution

### Initialize

```cpp
bool containsNearbyDuplicate(vector<int>& nums, int k) {
    unordered_set<int> window;
```

The set holds elements currently in the window (last k elements).

---

### Walk the array

```cpp
    for (int i = 0; i < nums.size(); i++) {
```

---

### Check for duplicate in the window

```cpp
        if (window.count(nums[i])) {
            return true;
        }
```

If `nums[i]` is already in the set, we found a duplicate
within distance k. The set only contains elements from
the last k indices — so the distance condition is guaranteed.

---

### Add current element to the window

```cpp
        window.insert(nums[i]);
```

---

### Evict the oldest element if window exceeds size k

```cpp
        if (window.size() > k) {
            window.erase(nums[i - k]);
        }
    }
```

The window should hold at most k elements.
When it grows to k+1, remove the element that entered k+1 steps ago.
That's `nums[i - k]` — the element that just fell out of the distance range.

> _"The window holds the last k visitors.
> When a new one arrives and the room is full,
> the oldest visitor is shown the door.
> If the new visitor was already inside — duplicate found."_

---

### No duplicate found

```cpp
    return false;
}
```

---

### 🎺 The Trial of the Nearby Duplicate

```cpp
int main() {
    vector<int> n1 = {1, 2, 3, 1};
    cout << boolalpha;
    cout << containsNearbyDuplicate(n1, 3) << endl; // expected: true

    vector<int> n2 = {1, 0, 1, 1};
    cout << containsNearbyDuplicate(n2, 1) << endl; // expected: true

    vector<int> n3 = {1, 2, 3, 1, 2, 3};
    cout << containsNearbyDuplicate(n3, 2) << endl; // expected: false

    return 0;
}
```

---

**Trace for nums = [1,2,3,1], k = 3:**

| i | nums[i] | In set? | Action | window | size |
|---|---------|---------|--------|--------|------|
| 0 | 1 | No | add | {1} | 1 |
| 1 | 2 | No | add | {1,2} | 2 |
| 2 | 3 | No | add | {1,2,3} | 3 |
| 3 | 1 | **Yes** | **return true** | — | — |

**Answer: true** ✓ (nums[0]=1 and nums[3]=1, distance 3 ≤ k=3)

---

**Trace for nums = [1,2,3,1,2,3], k = 2:**

| i | nums[i] | In set? | Action | window | size | Evict? |
|---|---------|---------|--------|--------|------|--------|
| 0 | 1 | No | add | {1} | 1 | No |
| 1 | 2 | No | add | {1,2} | 2 | No |
| 2 | 3 | No | add | {1,2,3} | 3 | Yes: evict nums[0]=1 → {2,3} |
| 3 | 1 | No | add | {2,3,1} | 3 | Yes: evict nums[1]=2 → {3,1} |
| 4 | 2 | No | add | {3,1,2} | 3 | Yes: evict nums[2]=3 → {1,2} |
| 5 | 3 | No | add | {1,2,3} | 3 | Yes: evict nums[3]=1 → {2,3} |

**Answer: false** ✓

Duplicates exist (1 at 0,3 and 2 at 1,4 and 3 at 2,5)
but all are distance 3 apart, which exceeds k=2.
By the time the duplicate arrives, the original has been evicted.

---

**Trace for nums = [1,0,1,1], k = 1:**

| i | nums[i] | In set? | Action | window |
|---|---------|---------|--------|--------|
| 0 | 1 | No | add | {1} |
| 1 | 0 | No | add, evict nums[0]=1 | {0} |
| 2 | 1 | No | add, evict nums[1]=0 | {1} |
| 3 | 1 | **Yes** | **return true** | — |

**Answer: true** ✓ (nums[2]=1 and nums[3]=1, distance 1 ≤ k=1)

---

## 🔍 Why a Set and Not a HashMap?

A hashmap (`value → index`) also works:

```cpp
bool containsNearbyDuplicate(vector<int>& nums, int k) {
    unordered_map<int, int> lastSeen;
    for (int i = 0; i < nums.size(); i++) {
        if (lastSeen.count(nums[i]) && i - lastSeen[nums[i]] <= k) {
            return true;
        }
        lastSeen[nums[i]] = i;
    }
    return false;
}
```

Check if the value was seen before AND within distance k.
Always update to the latest index.

**HashMap approach:** O(N) time, O(N) space (stores ALL elements).
**Set approach:** O(N) time, O(k) space (stores only last k elements).

The set approach uses less space when k << n.
The hashmap approach is simpler (no eviction logic).

Both are valid. Know both.

---

## 🔍 Connection to Contains Duplicate I and III

| Problem | Condition | Approach |
|---------|-----------|----------|
| Contains Duplicate I | Any duplicate exists | Hash set (no window) |
| **Contains Duplicate II** | **Duplicate within distance k** | **Sliding window set (size k)** |
| Contains Duplicate III | Duplicate within distance k AND value diff ≤ t | Bucket sort or ordered set |

Each adds one more constraint. The approach gets more sophisticated.

---

### 🧠 Memory of the Nearby Duplicate Law

-   **Sliding window hash set** of size ≤ k
-   **Check:** if `nums[i]` in set → return true (duplicate within k)
-   **Add:** insert `nums[i]`
-   **Evict:** if `size > k`, erase `nums[i - k]` (oldest element leaves)
-   **HashMap alternative:** store `value → last index`, check `i - lastSeen ≤ k`
-   **Time:** O(N). **Space:** O(min(N, k)).

Thus is remembered the saga of **Contains Duplicate II**,
where the Oracle maintained a window of the last k elements —
a hash set that held only the nearby visitors —
checking each new arrival against the current residents,
evicting the oldest when the room was full —
until a nearby duplicate was found
or the entire array was walked without one. 🪟🔍✨
