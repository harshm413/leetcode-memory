## 🪟📏 _The Fixed Window Census: The Count Subarrays With Size L and Sum K Saga_

> \_"The Oracle was given an array, a window size L, and a target sum K.
>
> She was commanded:
>
> **'Count the number of subarrays of EXACTLY size L
> whose sum equals K.'**
>
> This was the simplest sliding window problem:
>
> **Fixed-size window.**
>
> No expanding. No shrinking. The window is always size L.
> Slide it one step at a time. Check the sum at each position.
>
> The Oracle maintained a running sum:
> -   Add the new element entering from the right.
> -   Remove the old element leaving from the left.
> -   If sum equals K — count it.
>
> One pass. O(1) per step. Done."\_

---

This is the saga of **Count Subarrays With Size L and Sum K**.

Given an array `arr`, integer `L` (window size), and integer `K` (target sum):
-   Count subarrays of exactly length `L` whose sum equals `K`.

```
Input:  arr = [1, 2, 3, 4, 5], L = 3, K = 9
Output: 2   (subarrays: [2,3,4]=9, [4,5,?]... wait: [2,3,4]=9, [3,4,2]? No.
             Actually: [2,3,4]=9 ✓, [4,5,0]? No. Let me recount.
             [1,2,3]=6, [2,3,4]=9 ✓, [3,4,5]=12. Answer: 1.)

Input:  arr = [1, 2, 3, 4, 5], L = 3, K = 12
Output: 1   ([3, 4, 5] = 12)

Input:  arr = [2, 2, 2, 2, 2], L = 2, K = 4
Output: 4   (every consecutive pair sums to 4)

Input:  arr = [1, 1, 1, 1], L = 2, K = 2
Output: 3   ([1,1], [1,1], [1,1])
```

---

## 🧠 The Fixed-Size Sliding Window

Unlike variable-size windows (where left and right move independently),
a fixed-size window always has `right - left + 1 = L`.

**Setup:** compute the sum of the first L elements.
**Slide:** at each step, add the new right element, remove the old left element.
**Check:** if sum equals K, increment count.

No hashmap needed. No shrink logic. Just add one, remove one, check.

> _"The window doesn't grow or shrink.
> It slides like a frame across the array.
> At each position, the Oracle checks: does this frame sum to K?"_

---

### 📜 The Scroll of the Fixed Window Census

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 📏 The Fixed-Size Sliding Window Solution

### Compute the sum of the first window

```cpp
int countSubarrays(vector<int>& arr, int L, int K) {
    int n = arr.size();
    if (L > n) return 0;

    int windowSum = 0;
    for (int i = 0; i < L; i++) {
        windowSum += arr[i];
    }
```

Sum the first L elements. This is the initial window `[0, L-1]`.

---

### Check the first window

```cpp
    int count = 0;
    if (windowSum == K) count++;
```

Does the first window sum to K? If yes, count it.

---

### Slide the window one step at a time

```cpp
    for (int i = L; i < n; i++) {
        windowSum += arr[i];
        windowSum -= arr[i - L];
```

**Add** the new element entering from the right (`arr[i]`).
**Remove** the element leaving from the left (`arr[i - L]`).

The window has moved one position to the right.
Its size stays exactly L.

> _"One enters from the right. One leaves from the left.
> The window slides forward. Its size never changes."_

---

### Check the current window

```cpp
        if (windowSum == K) count++;
    }
    return count;
}
```

After each slide, check if the new sum equals K.

---

### 🎺 The Trial of the Fixed Window Census

```cpp
int main() {
    vector<int> a1 = {1, 2, 3, 4, 5};
    cout << countSubarrays(a1, 3, 9) << endl;  // expected: 1

    vector<int> a2 = {2, 2, 2, 2, 2};
    cout << countSubarrays(a2, 2, 4) << endl;  // expected: 4

    vector<int> a3 = {1, 1, 1, 1};
    cout << countSubarrays(a3, 2, 2) << endl;  // expected: 3

    vector<int> a4 = {3, 1, 4, 1, 5, 9};
    cout << countSubarrays(a4, 3, 10) << endl; // expected: 1

    return 0;
}
```

---

**Full trace for arr = [2, 2, 2, 2, 2], L = 2, K = 4:**

```
Initial window [0,1]: sum = 2+2 = 4 == K ✓. count = 1.

Slide:
  i=2: sum += 2, sum -= arr[0]=2. sum = 4 == K ✓. count = 2.
  i=3: sum += 2, sum -= arr[1]=2. sum = 4 == K ✓. count = 3.
  i=4: sum += 2, sum -= arr[2]=2. sum = 4 == K ✓. count = 4.
```

**Answer: 4** ✓ (every window of size 2 sums to 4)

---

**Trace for arr = [1, 2, 3, 4, 5], L = 3, K = 9:**

```
Initial window [0,1,2]: sum = 1+2+3 = 6 ≠ 9. count = 0.

Slide:
  i=3: sum += 4, sum -= 1. sum = 9 == K ✓. count = 1. (window [2,3,4])
  i=4: sum += 5, sum -= 2. sum = 12 ≠ 9. count = 1. (window [3,4,5])
```

**Answer: 1** ✓ (only [2,3,4] = 9)

---

**Trace for arr = [3, 1, 4, 1, 5, 9], L = 3, K = 10:**

```
Initial [0,1,2]: 3+1+4 = 8 ≠ 10.
i=3: 8+1-3 = 6 ≠ 10.
i=4: 6+5-1 = 10 == K ✓. count = 1. (window [4,1,5])
i=5: 10+9-4 = 15 ≠ 10.
```

**Answer: 1** ✓

---

## 🔍 Fixed Window vs Variable Window

| Aspect | Fixed Window (this) | Variable Window |
|--------|--------------------|-----------------| 
| Window size | Always L | Changes dynamically |
| Left pointer | `i - L` (implicit) | Moves independently |
| Shrink logic | None | `while` or `if` to shrink |
| Use case | "Exactly size L" problems | "Longest/shortest" problems |
| Complexity | Simpler | More complex |

Fixed window is the simplest sliding window pattern.
No decisions about when to shrink. Just slide.

---

## 🔍 Variations on This Problem

**Count subarrays of size L with sum ≥ K:**
Same sliding, check `windowSum >= K` instead of `== K`.

**Count subarrays of size L with max element = K:**
Use a deque (sliding window maximum) to track the max.

**Find the maximum sum subarray of size L:**
Same sliding, track `maxSum = max(maxSum, windowSum)`.

All use the same fixed-window slide. Different check at each position.

---

## 🔍 Why Not Prefix Sum?

Prefix sum also works:

```cpp
// prefixSum[i] = arr[0] + ... + arr[i-1]
// sum of [l, r] = prefixSum[r+1] - prefixSum[l]
// For window of size L ending at r: sum = prefixSum[r+1] - prefixSum[r+1-L]
```

Both are O(N). The sliding window approach is slightly more intuitive
and doesn't need the prefix array (O(1) extra space vs O(N)).

---

### 🧠 Memory of the Fixed Window Census Law

-   **Fixed-size window** of exactly L elements
-   **Setup:** sum first L elements
-   **Slide:** `windowSum += arr[i]`, `windowSum -= arr[i - L]`
-   **Check:** `if (windowSum == K) count++`
-   **No shrink logic** — window size never changes
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Count Subarrays With Size L and Sum K**,
where the Oracle slid a fixed frame across the array —
one element entering, one element leaving,
the sum updating in O(1) at each step —
counting every position where the frame's sum
matched the target exactly. 🪟📏✨
