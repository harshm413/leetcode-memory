## 📈🔍 _The Rising Chain (Binary Search): The LIS O(N log N) Saga_

> \_"The Oracle had found the LIS in O(N²).
>
> But the King brought arrays of size 100,000.
> O(N²) was too slow. She needed something faster.
>
> The Oracle invented a new technique:
>
> **Maintain a 'tails' array.**
> `tails[i]` = the SMALLEST possible tail element
> of all increasing subsequences of length `i+1`.
>
> For each new element:
> -   If it's LARGER than all tails → extend the LIS. Append it.
> -   Otherwise → find the first tail that is ≥ this element
>     and REPLACE it. This keeps tails as small as possible.
>
> The length of the tails array = the LIS length.
>
> Finding the replacement position? **Binary search.**
> Each element is processed in O(log N).
> Total: O(N log N).
>
> This was the patience sorting algorithm --
> named after the card game Patience (Solitaire)."\_

---

This is the saga of **LIS with Binary Search (DP-43)**.

Same problem:
-   Find the length of the longest strictly increasing subsequence.
-   But now in **O(N log N)** instead of O(N²).

```
Input:  nums = [10,9,2,5,3,7,101,18]  →  Output: 4
Input:  nums = [0,1,0,3,2,3]           →  Output: 4
Input:  nums = [7,7,7,7,7]             →  Output: 1
```

---

## 🧠 The Core Insight -- Why Track the Smallest Tail?

Consider two increasing subsequences of length 3:
-   [2, 5, 7] — tail is 7
-   [2, 3, 7] — tail is 7
-   [2, 3, 6] — tail is 6

Which is "better" for future extensions?
The one with the SMALLEST tail — [2, 3, 6].

Why? Because more future elements can extend it.
Any element > 6 extends [2, 3, 6].
Any element > 7 extends the others — but that's a stricter requirement.

**Smaller tail = more room to grow = better.**

So for each length, we only care about the subsequence
with the smallest possible tail. That's what `tails` tracks.

---

## 🧠 The Algorithm -- Step by Step

Maintain an array `tails` (initially empty).

For each element `num` in the array:

**Case 1: `num` > last element of tails (or tails is empty).**
`num` is larger than every existing tail.
It can extend the longest subsequence by 1.
**Append** `num` to tails.

**Case 2: `num` ≤ some element in tails.**
Find the FIRST element in tails that is ≥ `num`.
**Replace** it with `num`.

Why replace? We're not changing the LIS length.
We're making the tail SMALLER for that length.
This opens up more possibilities for future elements.

> _"Replacing doesn't destroy any existing subsequence.
> It says: 'There's now a subsequence of this length
> with a smaller tail. That's strictly better.'"_

**Finding the replacement position: `lower_bound` (binary search).**
`tails` is always sorted (we maintain this invariant).
Binary search finds the position in O(log N).

---

## 🧠 Why Tails Stays Sorted

Initially empty — trivially sorted.

**Append:** adds to the end, larger than everything → still sorted.
**Replace:** replaces an element with something ≤ it → still sorted.

The invariant is maintained by both operations.

---

### 📜 The Scroll of the Binary Search Rising Chain

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## 🔍 The O(N log N) Solution

### Initialize

```cpp
int lengthOfLIS(vector<int>& nums) {
    vector<int> tails;
```

`tails` starts empty. It will grow as we find longer subsequences.

---

### Process each element

```cpp
    for (int num : nums) {
```

---

### Find the position using binary search

```cpp
        auto it = lower_bound(tails.begin(), tails.end(), num);
```

`lower_bound` finds the first element in `tails` that is **≥ num**.

If `num` is larger than everything → `it` points to `tails.end()`.
If `num` equals or is smaller than some element → `it` points to that element.

This is O(log N) because `tails` is sorted.

---

### Append or replace

```cpp
        if (it == tails.end()) {
            tails.push_back(num);
        } else {
            *it = num;
        }
    }
```

**`it == tails.end()`:** `num` is larger than all tails.
It extends the LIS. Append it. Tails grows by 1.

**Otherwise:** `num` replaces the element at `it`.
The LIS length doesn't change. But the tail for that length
is now smaller (or equal) — better for future extensions.

> _"If the element rises above all tails — the chain grows.
> If not — it quietly improves an existing chain's tail,
> making room for future growth."_

---

### The answer

```cpp
    return tails.size();
}
```

The length of `tails` = the length of the LIS.

**Important:** `tails` is NOT the actual LIS.
It's a bookkeeping array that tracks the best possible tails.
The elements in `tails` may not form a valid subsequence.
But its LENGTH equals the LIS length. That's guaranteed.

---

### 🎺 The Trial

```cpp
int main() {
    vector<int> n1 = {10, 9, 2, 5, 3, 7, 101, 18};
    cout << lengthOfLIS(n1) << endl; // expected: 4

    vector<int> n2 = {0, 1, 0, 3, 2, 3};
    cout << lengthOfLIS(n2) << endl; // expected: 4

    vector<int> n3 = {7, 7, 7, 7, 7};
    cout << lengthOfLIS(n3) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for nums = [10, 9, 2, 5, 3, 7, 101, 18]:**

| Step | num | Action | tails after | Reasoning |
|------|-----|--------|-------------|-----------|
| 1 | 10  | append | [10] | Empty tails. Start the chain. |
| 2 | 9   | replace tails[0] | [9] | 9 < 10. Replace. Better tail for length 1. |
| 3 | 2   | replace tails[0] | [2] | 2 < 9. Replace. Even better tail for length 1. |
| 4 | 5   | append | [2, 5] | 5 > 2. Extends chain. Length 2 now. |
| 5 | 3   | replace tails[1] | [2, 3] | 3 < 5. Replace. Better tail for length 2. |
| 6 | 7   | append | [2, 3, 7] | 7 > 3. Extends chain. Length 3. |
| 7 | 101 | append | [2, 3, 7, 101] | 101 > 7. Extends chain. Length 4. |
| 8 | 18  | replace tails[3] | [2, 3, 7, 18] | 18 < 101. Replace. Better tail for length 4. |

**tails.size() = 4. Answer: 4** ✓

Note: `tails = [2, 3, 7, 18]` is a valid LIS here, but that's coincidence.
In general, tails is NOT the actual LIS.

---

**Trace for nums = [0, 1, 0, 3, 2, 3]:**

| Step | num | Action | tails after |
|------|-----|--------|-------------|
| 1 | 0   | append | [0] |
| 2 | 1   | append | [0, 1] |
| 3 | 0   | replace tails[0] | [0, 1] — 0 replaces 0 (same value, no change) |
| 4 | 3   | append | [0, 1, 3] |
| 5 | 2   | replace tails[2] | [0, 1, 2] |
| 6 | 3   | append | [0, 1, 2, 3] |

**Answer: 4** ✓

---

**Trace for nums = [7, 7, 7, 7, 7]:**

| Step | num | Action | tails after |
|------|-----|--------|-------------|
| 1 | 7   | append | [7] |
| 2 | 7   | replace tails[0] | [7] — lower_bound finds 7, replaces with 7 |
| 3 | 7   | replace tails[0] | [7] |
| 4 | 7   | replace tails[0] | [7] |
| 5 | 7   | replace tails[0] | [7] |

**Answer: 1** ✓

`lower_bound` finds the first element ≥ 7, which is tails[0] = 7.
It replaces 7 with 7 — no growth. Strictly increasing requires >,
and `lower_bound` correctly prevents equal elements from extending.

---

## 🔍 Why lower_bound and Not upper_bound?

**`lower_bound(num)`** = first element **≥ num**.
**`upper_bound(num)`** = first element **> num**.

For **strictly increasing** LIS, we use `lower_bound`.
If `num` equals a tail, `lower_bound` finds it and replaces it (no extension).
`upper_bound` would skip past it and extend — giving non-strictly increasing.

For **non-decreasing** LIS (allowing equal), use `upper_bound`.

---

## 🔍 Tails Is NOT the Actual LIS

```
nums = [3, 1, 4, 1, 5, 9, 2, 6]

After processing: tails = [1, 2, 6, 9]
Actual LIS: [1, 4, 5, 9] or [1, 4, 5, 6] or [3, 4, 5, 9]...
```

`tails` contains elements from different subsequences mixed together.
Its length is correct. Its contents are not a valid LIS.

To print the actual LIS with O(N log N), you need extra bookkeeping
(tracking which subsequence each element belongs to).
The O(N²) approach with parent array is simpler for printing.

---

## 🔍 The Patience Sorting Connection

This algorithm is called **patience sorting** because it mirrors
the card game Patience (Solitaire):

-   Each "pile" has cards in decreasing order (top is smallest).
-   A new card goes on the leftmost pile whose top is ≥ the card.
-   If no such pile exists, start a new pile.
-   Number of piles = LIS length.

`tails[i]` = the top card of pile `i`.
`lower_bound` = finding the leftmost valid pile.

---

## 🔍 O(N²) vs O(N log N) Comparison

| Aspect | O(N²) DP | O(N log N) Binary Search |
|--------|----------|--------------------------|
| Time | O(N²) | O(N log N) |
| Space | O(N) | O(N) |
| Print LIS | Easy (parent array) | Harder (extra tracking) |
| Concept | Simpler | Trickier |
| N ≤ 2500 | Fine | Overkill |
| N ≤ 10⁵ | TLE | Required |

Use O(N²) when you need to print the LIS or N is small.
Use O(N log N) when N is large and you only need the length.

---

### 🧠 Memory of the Binary Search Rising Chain Law

-   **`tails[i]`** = smallest tail of all increasing subsequences of length `i+1`
-   **Append** when `num > tails.back()` — LIS grows
-   **Replace** at `lower_bound(num)` otherwise — tail improves
-   **`tails` is always sorted** — binary search works
-   **`lower_bound`** for strictly increasing, `upper_bound` for non-decreasing
-   **`tails` is NOT the actual LIS** — only its length is correct
-   **Time:** O(N log N). **Space:** O(N).

Thus is remembered the saga of **LIS with Binary Search**,
where the Oracle maintained an array of smallest tails --
each new element either extended the chain
or quietly improved an existing tail --
binary search finding the right position in O(log N) --
until the length of the tails array revealed
the longest rising chain,
found not in quadratic time
but in the swift rhythm of N log N. 📈🔍✨
