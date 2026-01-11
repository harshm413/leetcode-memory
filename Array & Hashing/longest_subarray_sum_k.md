## 🌌📜 _The River of Shifting Tides: The Longest Subarray with Sum K (Positives + Negatives) Saga_

> \*"Beyond the Land of Pure Positives lay a far wilder river —
> a stream where currents surged forward
> and then pulled back without warning.
>
> Positives pushed the waters onward.
> Negatives dragged them back.
>
> The Oracle was summoned with a harder decree:
>
> **‘Find the longest continuous stretch of this river
> whose total flow equals exactly K.’**
>
> The old sliding-window maps failed here —
> for shrinking the banks did not always reduce the flow,
> and expanding them did not always increase it.
>
> So the Oracle forged a different tool:
> a **Chronicle of Prefix Sums**,
> remembering the first time each current appeared,
> so that distance — not repetition —
> would reveal the longest balanced stretch."\*

---

This is the saga of
**Longest Subarray with Sum K (Positives + Negatives)**.

You are given:

-   an array `nums` containing **positive, negative, and zero values**
-   an integer target `k`

Your task:

-   Find the **maximum length** of a subarray
-   whose sum is **exactly `k`**

---

## 🧠 The Oracle’s Core Insight — Prefix Sums Reveal Distance

Let:

```
prefixSum[i] = nums[0] + nums[1] + ... + nums[i]
```

If for two indices `i < j`:

```
prefixSum[j] - prefixSum[i] = k
```

Then the subarray `(i+1 ... j)` sums to `k`.

Rewritten as destiny:

```
prefixSum[i] = prefixSum[j] - k
```

So the Oracle needed to know:

> **When was a given prefix sum first seen?**

Because:

-   earlier index → **longer subarray**

Thus she recorded **only the first occurrence**
of every prefix sum.

---

### 📜 The Scroll of Unstable Currents

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## 🔮 The Oracle’s Chronicle Ritual

_Record the first sighting of every current_

```cpp
int longestSubarrayWithSumK(vector<int>& nums, int k) {
    unordered_map<int, int> firstSeen;
    int sum = 0;
    int maxLen = 0;
```

-   `sum` → current prefix sum
-   `firstSeen[sum]` → earliest index where this sum appeared

---

### 🌊 Walk the River One Step at a Time

```cpp
    for (int i = 0; i < nums.size(); i++) {
        sum += nums[i];
```

The river’s flow changed unpredictably.

---

### ✨ Case 1 — From the Beginning to Here

```cpp
        if (sum == k) {
            maxLen = i + 1;
        }
```

If the entire stretch from index `0` to `i` balanced to `k`,
the Oracle recorded its full length.

---

### 🔍 Case 2 — Look for a Matching Past Current

```cpp
        if (firstSeen.count(sum - k)) {
            maxLen = max(maxLen, i - firstSeen[sum - k]);
        }
```

If `(sum - k)` had appeared before,
then the stretch between that past index and now
formed a balanced subarray.

---

### 📘 Record the First Appearance Only

```cpp
        if (!firstSeen.count(sum)) {
            firstSeen[sum] = i;
        }
    }
    return maxLen;
}
```

The Oracle never overwrote earlier sightings —
for the **earliest index yields the longest distance**.

---

### 🎺 The Trial of the Shifting River

```cpp
int main() {
    vector<int> nums = {1, -1, 5, -2, 3};
    int k = 3;

    cout << longestSubarrayWithSumK(nums, k) << endl; // expected: 4
    return 0;
}
```

The Oracle discovered:

-   Subarray `{1, -1, 5, -2}`
-   Sum = 3
-   Length = **4**

No longer balanced stretch existed.

---

### 🧠 Memory of the Shifting-Tide Law

-   Sliding window fails with negatives
-   Use **prefix sum + hash map**
-   Store **first occurrence** of each prefix sum
-   Check if `(currentSum - k)` exists
-   Earlier index → longer subarray
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Longest Subarray with Sum K (Positives + Negatives)**,
where the Oracle masters a river of shifting tides,
tracking echoes of past currents —
and measuring the longest distance
between two perfectly balanced moments in time. 🌌✨
