## 🔝🏆 _The Tournament of Loudest Voices: The Top K Frequent Elements (Heap) Saga_

> \_"In the Valley of Echoes,
> numbers called and recalled endlessly --
> some whispered once and faded,
> others thundered again and again,
> their voices shaping the hills.
>
> The Oracle was commanded:
>
> **'Find the K numbers that echo the loudest --
> the K most frequent elements.'**
>
> The Oracle devised a two-step ritual:
>
> **Step One -- Count every voice.**
> Walk the array once with a hash map.
> Record how many times each number appeared.
>
> **Step Two -- Run a tournament of K seats.**
> Maintain a Min-Heap of size K,
> keyed on frequency.
> The weakest voice among the K champions
> always sits at the top.
> When a louder voice arrives and the heap overflows --
> the weakest is expelled.
>
> After all voices have been judged,
> the K survivors are the answer.
>
> Count first. Then select with a heap.
> O(N log K)."\_

---

This is the saga of **Top K Frequent Elements (Heap Approach)**.

Given an integer array `nums` and an integer `k`:

-   Return the `k` most frequent elements.
-   Answer may be returned in **any order**.

```
Input:  nums = [1,1,1,2,2,3], k = 2
Output: [1, 2]

Input:  nums = [1], k = 1
Output: [1]

Input:  nums = [1,1,2,2,3,3,3], k = 2
Output: [3, 1] or [3, 2] (any valid top 2)
```

---

## 🧠 The Oracle's Core Insight -- Count Then Select

The problem has two phases:

**Phase 1 -- Frequency Count:** O(N)
Walk the array. Use a hash map to count occurrences.

**Phase 2 -- Top K Selection:** O(N log K)
Use a min-heap of size K, keyed on frequency.
Push each `{frequency, element}` pair.
When the heap exceeds K -- pop the minimum frequency (weakest voice).

After all entries, the heap holds the K most frequent elements.

**Why min-heap for finding K largest frequencies?**

Same pattern as Kth Largest in a Stream:
we want to evict the LEAST frequent among our K candidates.
The min-heap always shows us the least frequent at the top.

```
Time:  O(N log K) -- N for counting, D log K for heap (D = distinct elements)
Space: O(N) -- hash map + heap
```

---

### 📜 The Scroll of the Echoing Valley

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <unordered_map>
using namespace std;
```

---

## 📊 Phase One -- Count Every Voice

```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    for (int x : nums) {
        freq[x]++;
    }
```

The Oracle opened the Ledger of Echoes --
counting how many times each number appeared.

For `[1,1,1,2,2,3]`: `freq = {1:3, 2:2, 3:1}`.

One pass. O(N).

---

## 🏆 Phase Two -- Min-Heap Tournament of K Seats

```cpp
    // Min-heap of {frequency, element}
    // Top = smallest frequency among K candidates
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> minHeap;
```

The Oracle summoned a **Min-Heap** of `{frequency, element}` pairs.

`greater<pair<int,int>>` flipped C++'s default max-heap into a min-heap.
The pair with the **smallest frequency** always floated to the top --
the weakest voice among the K champions,
always ready to be expelled.

---

### 🔁 Push Every Element, Expel the Weakest

```cpp
    for (auto& [val, cnt] : freq) {
        minHeap.push({cnt, val});
        if ((int)minHeap.size() > k) {
            minHeap.pop();
        }
    }
```

Every element-frequency pair was pushed into the heap.

When the heap exceeded K entries,
the weakest voice (smallest frequency at the top) was expelled.

After processing all distinct elements,
the heap held exactly the K most frequent.

> _"Only K thrones exist on the summit.
> The moment a louder voice arrives and fills the K+1th seat,
> the quietest among them is cast down."_

---

## 🏁 Collect the K Champions

```cpp
    vector<int> result;
    while (!minHeap.empty()) {
        result.push_back(minHeap.top().second);
        minHeap.pop();
    }
    return result;
}
```

The K champions were drained from the heap.
Each entry's `.second` held the element value.

Order didn't matter -- the problem accepted any arrangement.

---

### 🎺 The Trial of the Echoing Valley

```cpp
int main() {
    vector<int> nums1 = {1, 1, 1, 2, 2, 3};
    auto r1 = topKFrequent(nums1, 2);
    for (int x : r1) cout << x << " "; cout << endl;
    // expected: 1 2 (any order)

    vector<int> nums2 = {1};
    auto r2 = topKFrequent(nums2, 1);
    for (int x : r2) cout << x << " "; cout << endl;
    // expected: 1

    vector<int> nums3 = {1,1,2,2,3,3,3};
    auto r3 = topKFrequent(nums3, 2);
    for (int x : r3) cout << x << " "; cout << endl;
    // expected: 3 and one of {1,2}

    return 0;
}
```

---

**Full trace for `[1,1,1,2,2,3]`, k=2:**

**Phase 1 -- Frequency count:**
`freq = {1:3, 2:2, 3:1}`

**Phase 2 -- Min-heap tournament:**

| Entry pushed | Heap after push            | Size>2? | Pop (weakest)  | Heap after         |
| ------------ | -------------------------- | ------- | -------------- | ------------------ |
| {3, 1}       | [{3,1}]                    | No      | --             | [{3,1}]            |
| {2, 2}       | [{2,2}, {3,1}]             | No      | --             | [{2,2}, {3,1}]     |
| {1, 3}       | [{1,3}, {3,1}, {2,2}]      | Yes     | {1,3} (freq 1) | [{2,2}, {3,1}]     |

Final heap: `{2,2}` and `{3,1}` -- elements `2` and `1`.

**Answer: [2, 1] or [1, 2]** ✓

Element `3` with frequency 1 was the weakest -- expelled.
Elements `1` (freq 3) and `2` (freq 2) survived as the top 2.

---

**Full trace for `[1,1,2,2,3,3,3]`, k=2:**

**Frequencies:** `{1:2, 2:2, 3:3}`

| Entry pushed | Heap after push            | Size>2? | Pop            | Heap after         |
| ------------ | -------------------------- | ------- | -------------- | ------------------ |
| {2, 1}       | [{2,1}]                    | No      | --             | [{2,1}]            |
| {2, 2}       | [{2,1}, {2,2}]             | No      | --             | [{2,1}, {2,2}]     |
| {3, 3}       | [{2,1}, {2,2}, {3,3}]      | Yes     | {2,1} (freq 2) | [{2,2}, {3,3}]     |

**Answer: [2, 3] or [3, 2]** ✓

Elements `1` and `2` both had frequency 2.
When `3` (freq 3) entered, one of the freq-2 elements was expelled.
Which one depends on heap internals -- both are valid answers.

---

**Trace for `[1]`, k=1:**

Frequencies: `{1:1}`. Push `{1,1}`. Heap size = 1 = k. No pop.

**Answer: [1]** ✓

---

**Trace for `[4,4,4,4]`, k=1:**

Frequencies: `{4:4}`. Only one distinct element. Push it. Done.

**Answer: [4]** ✓

---

## 🔍 Why Min-Heap and Not Max-Heap?

A max-heap would put the MOST frequent on top.
To get the top K, you'd pop K times from a max-heap of all D elements.
That's O(D + K log D) -- fine, but the heap holds all D elements.

A min-heap of size K holds only K elements at any time.
The LEAST frequent among the K candidates sits on top --
ready to be expelled when a more frequent element arrives.

For large D and small K, the min-heap approach is more space-efficient
and avoids building a heap of all distinct elements.

---

## 🔄 Comparison with Bucket Sort Approach

| Min-Heap (this approach)          | Bucket Sort                       |
| --------------------------------- | --------------------------------- |
| O(N + D log K) time               | O(N) time                         |
| O(N) space                        | O(N) space                        |
| Works for any K                   | Works for any K                   |
| Straightforward heap pattern      | Requires bucket array of size N+1 |
| Better when D >> K                | Strictly faster (linear)          |

The bucket sort approach (in the Array & Hashing saga)
creates `n+1` buckets indexed by frequency,
places elements into their frequency bucket,
then walks from highest bucket downward collecting K elements.
It's O(N) -- strictly faster.

But the heap approach is the standard "Top K" pattern
that appears across many problems.
Master it here, reuse it everywhere.

---

### 🧠 Memory of the Loudest Voices Law

-   **Phase 1:** count frequencies with `unordered_map` -- O(N)
-   **Phase 2:** min-heap of `{frequency, element}` pairs, size K
    -   Push each distinct element's `{freq, val}`
    -   If `heap.size() > k` → pop minimum frequency (weakest expelled)
    -   After all entries → heap holds K most frequent
-   **Min-heap** because we expel the LEAST frequent among K candidates
-   `greater<pair<int,int>>` flips C++ max-heap to min-heap
-   Answer may be returned in any order
-   **Time:** O(N + D log K) where D = distinct elements
-   **Space:** O(N) -- hash map + heap
-   **Edge cases:**
    -   k = 1 → return the single most frequent
    -   All same frequency → any K elements are valid
    -   Single element array → return that element
    -   k = D (all distinct) → return all elements

Thus is remembered the saga of **Top K Frequent Elements (Heap)**,
where the Oracle counted every voice in the valley,
then ran a tournament of K seats --
pushing each voice's frequency into a min-heap,
expelling the quietest whenever the roster overflowed --
until only the K loudest voices remained,
their echoes ringing across the valley. 🔝🏆✨
