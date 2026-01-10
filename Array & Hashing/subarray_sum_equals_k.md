## 🔮🧮 _The Ledger of Balanced Currents: The Subarray Sum Equals K Saga_

> \*"In the River of Numbers,
> values flowed forward without pause —
> some rising, some falling,
> positives and negatives entwined like unseen currents.
>
> The Oracle was asked a subtle question:
>
> **‘How many continuous stretches of this river
> sum exactly to the sacred value K?’**
>
> The answer could not be found
> by brute-force wading through every possible stretch —
> the river was too long, the currents too tangled.
>
> Instead, the Oracle forged a **ledger of past currents**,
> where every cumulative flow was remembered,
> and balance could be recognized
> the instant it appeared."\*

---

This is the saga of **Subarray Sum Equals K**.

You are given:

-   an integer array `nums`
-   an integer target `k`

Your task:

-   Count the number of **continuous subarrays**
-   whose elements sum to **exactly `k`**

Numbers may be **positive, negative, or zero**,
making sliding windows unreliable.

Thus the Oracle used:

### ✨ Prefix Sum + Hash Map

---

## 🧠 The Oracle’s Core Insight

Let:

```
prefixSum[i] = nums[0] + nums[1] + ... + nums[i]
```

If at some point:

```
prefixSum[j] - prefixSum[i] = k
```

Then the subarray `(i+1 ... j)` sums to `k`.

Rewriting destiny:

```
prefixSum[i] = prefixSum[j] - k
```

So the Oracle only needed to know:

> **How many times has `(currentSum - k)` appeared before?**

Thus began the ledger-based counting.

---

### 📜 The Scroll of Flowing Numbers

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## 🔮 The Oracle’s Ledger Ritual

_Count balanced subarrays in one pass_

```cpp
int subarraySum(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    freq[0] = 1;
```

Before the river began flowing,
the Oracle recorded a **zero-sum once** —
representing the empty prefix.

This allowed subarrays starting at index `0`
to be counted correctly.

---

### 🌊 Walk the River, One Number at a Time

```cpp
    int sum = 0;
    int count = 0;
```

-   `sum` → current prefix sum
-   `count` → number of valid subarrays found

---

### ⚖️ Check for Balanced Currents

```cpp
    for (int x : nums) {
        sum += x;
```

Each new number changed the river’s flow.

---

```cpp
        if (freq.count(sum - k)) {
            count += freq[sum - k];
        }
```

If `(sum - k)` had appeared before,
then every such occurrence formed
a subarray ending **right here**
with total flow equal to `k`.

The Oracle added all of them at once.

---

### 📒 Record the Current Flow

```cpp
        freq[sum]++;
    }
```

The current prefix sum was written into the ledger,
ready to help future balances.

---

### 🏁 Return the Final Count

```cpp
    return count;
}
```

All balanced stretches of the river were revealed.

---

### 🎺 The Trial of the Balanced River

```cpp
int main() {
    vector<int> nums = {1, 2, 3};
    int k = 3;

    cout << subarraySum(nums, k) << endl; // expected: 2
    return 0;
}
```

The Oracle found two balanced stretches:

-   `[1, 2]`
-   `[3]`

The river spoke truth.

---

### 🧠 Memory of the Balanced-Ledger Law

-   Use prefix sums to track cumulative flow
-   Store frequencies of prefix sums in a hash map
-   If `sum - k` exists, it forms valid subarrays
-   Initialize `freq[0] = 1` for subarrays starting at index 0
-   Handles negatives, positives, and zeros
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of **Subarray Sum Equals K**,
where the Oracle does not chase every possible stretch of the river,
but instead listens to echoes of past currents —
and counts balance the moment it appears. 🔮🧮✨
