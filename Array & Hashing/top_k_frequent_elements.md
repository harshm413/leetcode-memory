## 🔝🗣️ _The Summit of Voices: The Top K Frequent Elements Saga_

> \_"In the Valley of Echoes,
> numbers called and recalled endlessly --
> some whispered once and faded,
> others thundered again and again,
> their voices shaping the hills.
>
> The Oracle was commanded:
>
> **'Find the K numbers that echo the loudest --
> the K most frequent elements in this valley.'**
>
> The naive approach was clear:
> count every number's frequency,
> sort all numbers by frequency,
> and pick the top K.
> That was O(N log N) -- the cost of a full sort.
>
> But the Oracle knew two faster paths:
>
> **Path One -- Min-Heap of Size K:**
> Maintain a leaderboard of exactly K loudest voices.
> The heap's top is always the weakest among the K.
> When a louder voice arrives, the weakest is expelled.
> O(N log K) -- faster when K is small.
>
> **Path Two -- Bucket Sort by Frequency:**
> Create buckets indexed by frequency (0 to N).
> Place each number into its frequency's bucket.
> Walk the buckets from highest to lowest,
> collecting numbers until K are gathered.
> O(N) -- the fastest possible.
>
> The Oracle mastered both."\_

---

This is the saga of **Top K Frequent Elements**.

You are given an integer array `nums` and an integer `k`.

Your task:

-   Return the `k` most frequent elements.
-   The answer may be returned in **any order**.

```
Input:  nums = [1,1,1,2,2,3], k = 2
Output: [1, 2]

Input:  nums = [1], k = 1
Output: [1]

Input:  nums = [1,1,2,2,3,3,3], k = 2
Output: [3, 1] or [3, 2] or [1, 3] etc. (any valid top 2)
```

---

## 🧠 The Oracle's Core Insight -- Count First, Then Select

Both approaches share the same first step:

> **Count the frequency of every element using a hash map.**

After counting, the question becomes:
"How do we efficiently find the K elements with the highest frequencies?"

```
Path One -- Min-Heap:
  Push each {element, frequency} into a min-heap of size K.
  If heap exceeds K, pop the minimum (weakest voice).
  After all elements, the heap holds the K most frequent.
  O(N log K) time, O(N) space.

Path Two -- Bucket Sort:
  Create an array of buckets where bucket[f] = list of elements with frequency f.
  Walk from the highest bucket downward, collecting until K elements gathered.
  O(N) time, O(N) space.
```

---

### 📜 The Scroll of the Valley

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;
```

---

## 🍟 Path One -- Min-Heap of K Champions

```cpp
vector<int> topKFrequent_heap(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    for (int x : nums) freq[x]++;
```

The Oracle opened the Ledger of Echoes --
counting how many times each number appeared.

For `[1,1,1,2,2,3]`: `freq = {1:3, 2:2, 3:1}`.

---

```cpp
    auto cmp = [](pair<int,int>& a, pair<int,int>& b) {
        return a.second > b.second;
    };
    priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> minHeap(cmp);
```

The Oracle summoned a **Min-Heap** of `{element, frequency}` pairs.

The comparator `a.second > b.second` made the pair with the
**smallest frequency** float to the top --
so the weakest voice among the K champions
was always ready to be expelled.

---

```cpp
    for (auto& p : freq) {
        minHeap.push({p.first, p.second});
        if ((int)minHeap.size() > k) {
            minHeap.pop();
        }
    }
```

Every element-frequency pair was pushed into the heap.

When the heap exceeded K entries,
the weakest voice (smallest frequency at the top) was expelled.

After processing all entries,
the heap held exactly the K most frequent elements.

> _"Only K thrones exist on the summit.
> The moment a louder voice arrives and fills the K+1th seat,
> the quietest among them is cast down."_

---

```cpp
    vector<int> result;
    while (!minHeap.empty()) {
        result.push_back(minHeap.top().first);
        minHeap.pop();
    }
    return result;
}
```

The K champions were collected from the heap.
Order did not matter -- the problem accepted any arrangement.

---

## 🪣 Path Two -- Bucket Sort by Frequency

_O(N) -- the fastest possible_

```cpp
vector<int> topKFrequent_bucket(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    for (int x : nums) freq[x]++;
```

Same first step -- count frequencies.

---

```cpp
    int n = nums.size();
    vector<vector<int>> buckets(n + 1);
```

The Oracle created `n + 1` buckets,
indexed from `0` to `n`.

`buckets[f]` would hold all elements whose frequency is exactly `f`.

Why `n + 1`? Because the maximum possible frequency
of any element is `n` (if the entire array is one number).

---

```cpp
    for (auto& p : freq) {
        buckets[p.second].push_back(p.first);
    }
```

Each element was placed into the bucket matching its frequency.

For `{1:3, 2:2, 3:1}`:
-   `buckets[3] = [1]`
-   `buckets[2] = [2]`
-   `buckets[1] = [3]`

---

```cpp
    vector<int> result;
    for (int f = n; f >= 0 && (int)result.size() < k; f--) {
        for (int x : buckets[f]) {
            result.push_back(x);
            if ((int)result.size() == k) break;
        }
    }
    return result;
}
```

The Oracle walked the buckets from highest frequency downward.

At each bucket, every element inside was collected
until K elements had been gathered.

The highest-frequency elements were picked first --
exactly the K most frequent.

> _"The loudest voices live in the highest buckets.
> Walk downward from the summit,
> gathering champions until K thrones are filled."_

---

### 🎺 The Trial of the Valley

```cpp
int main() {
    vector<int> nums1 = {1, 1, 1, 2, 2, 3};
    auto ans1 = topKFrequent_heap(nums1, 2);
    for (int x : ans1) cout << x << " ";
    cout << endl; // expected: 1 2 (any order)

    vector<int> nums2 = {1, 1, 1, 2, 2, 3};
    auto ans2 = topKFrequent_bucket(nums2, 2);
    for (int x : ans2) cout << x << " ";
    cout << endl; // expected: 1 2 (any order)

    return 0;
}
```

---

**Heap trace for `[1,1,1,2,2,3]`, k = 2:**

Frequencies: `{1:3, 2:2, 3:1}`

| Entry pushed | Heap after push       | Size > 2? | Pop (weakest) | Heap after   |
| ------------ | --------------------- | --------- | ------------- | ------------ |
| {1, 3}       | [{1,3}]               | No        | --            | [{1,3}]      |
| {2, 2}       | [{2,2}, {1,3}]        | No        | --            | [{2,2},{1,3}]|
| {3, 1}       | [{3,1}, {1,3}, {2,2}] | Yes       | {3,1} (freq 1)| [{2,2},{1,3}]|

Final heap: `{2,2}` and `{1,3}` -- the two most frequent.

**Answer: [2, 1] or [1, 2]** ✓

---

**Bucket trace for `[1,1,1,2,2,3]`, k = 2:**

Frequencies: `{1:3, 2:2, 3:1}`

```
buckets[0] = []
buckets[1] = [3]
buckets[2] = [2]
buckets[3] = [1]
buckets[4] = []
buckets[5] = []
buckets[6] = []
```

Walk from bucket 6 downward:
-   bucket[6]: empty
-   bucket[5]: empty
-   bucket[4]: empty
-   bucket[3]: pick `1` -- result = [1], need 1 more
-   bucket[2]: pick `2` -- result = [1, 2], done

**Answer: [1, 2]** ✓

---

## ⚖️ Heap vs Bucket -- When to Choose Which

**Min-Heap (O(N log K)):**
-   Better when K is much smaller than N
-   Straightforward to implement
-   Works well in streaming scenarios

**Bucket Sort (O(N)):**
-   Strictly faster -- linear time regardless of K
-   Uses more memory (N+1 buckets, most empty)
-   Perfect for interview "better than O(N log N)" follow-ups

Both are valid. The bucket approach is the optimal answer
when the interviewer asks "can you do better than O(N log K)?"

---

### 🧠 Memory of the Summit Law

-   **Step 1:** Count frequencies with `unordered_map` -- O(N)
-   **Min-Heap approach:**
    -   Min-heap of size K -- top = weakest among K champions
    -   Push each entry, pop when size > K
    -   O(N log K) time, O(N) space
-   **Bucket Sort approach:**
    -   `buckets[f]` = list of elements with frequency `f`
    -   Walk from highest bucket downward, collect K elements
    -   O(N) time, O(N) space
-   Answer may be returned in any order
-   **Edge cases:**
    -   K = 1 -- return the single most frequent element
    -   All elements same frequency -- any K of them are valid
    -   Single element array -- return that element

Thus is remembered the saga of **Top K Frequent Elements**,
where the Oracle counted every voice in the valley,
then crowned the K loudest --
either by maintaining a Min-Heap tournament
that expelled the weakest challenger at every step,
or by sorting voices into frequency buckets
and walking downward from the summit,
gathering champions until K thrones were filled. 🔝🗣️✨
