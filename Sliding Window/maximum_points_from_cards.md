## 🪟🃏 _The Card Trick: The Maximum Points You Can Obtain from Cards Saga_

> \_"The Oracle was given a row of cards, each with a point value.
>
> She was commanded:
>
> **'Pick exactly K cards — from the LEFT end, the RIGHT end,
> or a combination of both. Maximize the total points.'**
>
> She could take 3 from the left and 2 from the right.
> Or 0 from the left and 5 from the right.
> Any split — as long as the total cards taken = K.
>
> The Oracle flipped the problem:
>
> **If I take K cards from the ends,
> I'm LEAVING a contiguous subarray of size N-K in the middle.**
>
> Maximizing the sum of cards taken =
> Minimizing the sum of cards left behind.
>
> **Find the minimum sum window of size N-K.
> Answer = totalSum - minWindowSum.**
>
> A fixed-size sliding window problem in disguise."\_

---

This is the saga of **Maximum Points You Can Obtain from Cards (LeetCode 1423)**.

Given an array `cardPoints` and integer `k`:
-   Take exactly `k` cards from the left end, right end, or both.
-   Return the maximum sum of points.

```
Input:  cardPoints = [1, 2, 3, 4, 5, 6, 1], k = 3
Output: 12  (take [1,6,5] from right... or [6,5,1]? Actually: take 1 from left + 2 from right = 1+1+6=8? No. Take 3 from right = 5+6+1=12.)

Input:  cardPoints = [2, 2, 2], k = 2
Output: 4   (take any 2)

Input:  cardPoints = [9, 7, 7, 9, 7, 7, 9], k = 7
Output: 55  (take all)
```

---

## 🧠 The Flip — Minimize the Middle Instead

Taking K cards from the ends = leaving N-K cards in the middle.

```
[  taken  |  left behind (N-K)  |  taken  ]
   left         middle              right
```

The middle is always a CONTIGUOUS subarray of size N-K.

**Maximize taken = Maximize (totalSum - middle) = Minimize middle.**

So: find the minimum sum subarray of size exactly N-K.
Answer = totalSum - that minimum.

> _"The Oracle doesn't think about which cards to TAKE.
> She thinks about which cards to LEAVE.
> The cheapest middle = the most expensive ends."_

---

## 🧠 Why This Is a Fixed-Size Window

The middle has exactly `N - K` elements. Fixed size.
Slide a window of size `N - K` across the array.
Track the minimum sum. Done.

---

### 📜 The Scroll of the Card Trick

```cpp
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;
```

---

## 🃏 The Fixed-Window Solution

### Compute total sum

```cpp
int maxScore(vector<int>& cardPoints, int k) {
    int n = cardPoints.size();
    int totalSum = accumulate(cardPoints.begin(), cardPoints.end(), 0);
```

Sum of all cards. The answer = totalSum - minimum middle.

---

### Edge case: take all cards

```cpp
    int windowSize = n - k;
    if (windowSize == 0) return totalSum;
```

If K = N, take everything. No middle exists.

---

### Compute sum of first window

```cpp
    int windowSum = 0;
    for (int i = 0; i < windowSize; i++) {
        windowSum += cardPoints[i];
    }
    int minWindowSum = windowSum;
```

First window: `[0, windowSize-1]`. This is the initial "middle."

---

### Slide the window, track minimum

```cpp
    for (int i = windowSize; i < n; i++) {
        windowSum += cardPoints[i];
        windowSum -= cardPoints[i - windowSize];
        minWindowSum = min(minWindowSum, windowSum);
    }
```

Slide one step at a time. Add new right, remove old left.
Track the minimum sum seen across all positions.

---

### Answer = total minus cheapest middle

```cpp
    return totalSum - minWindowSum;
}
```

> _"The cheapest middle is found.
> Everything else — the ends — is the maximum we can take."_

---

### 🎺 The Trial of the Card Trick

```cpp
int main() {
    vector<int> c1 = {1, 2, 3, 4, 5, 6, 1};
    cout << maxScore(c1, 3) << endl; // expected: 12

    vector<int> c2 = {2, 2, 2};
    cout << maxScore(c2, 2) << endl; // expected: 4

    vector<int> c3 = {9, 7, 7, 9, 7, 7, 9};
    cout << maxScore(c3, 7) << endl; // expected: 55

    vector<int> c4 = {1, 79, 80, 1, 1, 1, 200, 1};
    cout << maxScore(c4, 3) << endl; // expected: 202

    return 0;
}
```

---

**Trace for cardPoints = [1, 2, 3, 4, 5, 6, 1], k = 3:**

```
totalSum = 1+2+3+4+5+6+1 = 22
windowSize = 7 - 3 = 4

Initial window [0..3]: sum = 1+2+3+4 = 10. minWindowSum = 10.

Slide:
  i=4: sum = 10+5-1 = 14. min = 10.
  i=5: sum = 14+6-2 = 18. min = 10.
  i=6: sum = 18+1-3 = 16. min = 10.

minWindowSum = 10 (window [0..3] = [1,2,3,4]).
Answer = 22 - 10 = 12.
```

**Answer: 12** ✓ (take cards 5, 6, 1 from the right)

---

**Trace for cardPoints = [1, 79, 80, 1, 1, 1, 200, 1], k = 3:**

```
totalSum = 364
windowSize = 8 - 3 = 5

Initial window [0..4]: 1+79+80+1+1 = 162. min = 162.
i=5: 162+1-1 = 162. min = 162.
i=6: 162+200-79 = 283. min = 162.
i=7: 283+1-80 = 204. min = 162.

Answer = 364 - 162 = 202.
```

**Answer: 202** ✓ (take 1 from left + 200, 1 from right = 1+200+1=202)

---

## 🔍 The Alternative — Direct Prefix + Suffix Approach

Instead of finding the minimum middle, directly try all splits:

```cpp
int maxScore(vector<int>& cardPoints, int k) {
    int n = cardPoints.size();
    vector<int> prefix(k + 1, 0), suffix(k + 1, 0);
    for (int i = 0; i < k; i++) prefix[i + 1] = prefix[i] + cardPoints[i];
    for (int i = 0; i < k; i++) suffix[i + 1] = suffix[i] + cardPoints[n - 1 - i];

    int ans = 0;
    for (int left = 0; left <= k; left++) {
        int right = k - left;
        ans = max(ans, prefix[left] + suffix[right]);
    }
    return ans;
}
```

Try all splits: 0 from left + K from right, 1 from left + K-1 from right, etc.
O(K) time, O(K) space.

Both approaches are O(N). The sliding window approach is slightly cleaner
(no prefix/suffix arrays needed).

---

## 🔍 The Two Approaches Compared

| Approach | Idea | Time | Space |
|----------|------|------|-------|
| **Min window (this)** | Minimize middle of size N-K | O(N) | O(1) |
| Prefix + Suffix | Try all left/right splits | O(N) | O(K) |

Both are valid. The min-window approach is more elegant.
The prefix-suffix approach is more intuitive.

---

### 🧠 Memory of the Card Trick Law

-   **Flip the problem:** maximize ends = minimize middle
-   **Middle** = contiguous subarray of size N-K
-   **Fixed-size sliding window** to find minimum sum of size N-K
-   **Answer** = totalSum - minWindowSum
-   **Edge case:** K = N → take all, return totalSum
-   **Alternative:** prefix + suffix sums, try all splits
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Maximum Points from Cards**,
where the Oracle flipped the problem —
instead of maximizing what she took from the ends,
she minimized what she left in the middle —
a fixed-size window sliding across the array,
tracking the cheapest middle,
and subtracting it from the total
to reveal the maximum points. 🪟🃏✨
