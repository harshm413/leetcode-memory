## 🔄🔁 _The Three Reversals: The Rotate Array Saga_

> \_"The Oracle was given an array and a number `k`.
>
> She was commanded:
>
> **'Rotate the array to the RIGHT by k steps.
> The last k elements move to the front.
> Do it in-place.'**
>
> `[1,2,3,4,5,6,7]` rotated by 3 → `[5,6,7,1,2,3,4]`.
>
> The last 3 elements `[5,6,7]` moved to the front.
> The first 4 elements `[1,2,3,4]` shifted to the back.
>
> The Oracle knew two approaches:
>
> **The quick way:** use extra space. Place each element
> at its new position `(i + k) % n` in a temp array. Copy back.
> O(N) time, O(N) space. Simple but not in-place.
>
> **The elegant way:** three reversals. No extra array.
> O(N) time, O(1) space. The true in-place solution."\_

---

This is the saga of **Rotate Array (LeetCode 189)**.

Given an array `nums` and integer `k`:
-   Rotate the array to the right by `k` steps, in-place.

```
Input:  nums = [1,2,3,4,5,6,7], k = 3
Output: [5,6,7,1,2,3,4]

Input:  nums = [-1,-100,3,99], k = 2
Output: [3,99,-1,-100]

Input:  nums = [1,2,3], k = 5
Output: [2,3,1]  (k=5 % 3 = 2 effective rotations)
```

---

## 🧠 First — Why k = k % n?

If `k >= n`, rotating by `n` steps brings the array back to its original position.
So only `k % n` steps matter.

`k = 5, n = 3`: 5 % 3 = 2. Rotating by 5 = rotating by 2.
`k = 7, n = 7`: 7 % 7 = 0. No rotation needed.

---

## 💡 Approach 1 — Extra Space (Quick Mention)

Before the elegant solution, the straightforward one:

```cpp
void rotate(vector<int>& nums, int k) {
    int n = nums.size();
    k = k % n;
    vector<int> temp(n);
    for (int i = 0; i < n; i++) {
        temp[(i + k) % n] = nums[i];
    }
    nums = temp;
}
```

Each element at index `i` moves to index `(i + k) % n`.
The `% n` wraps around — element at index 5 with k=3 and n=7
goes to index (5+3)%7 = 1.

```
nums = [1,2,3,4,5,6,7], k = 3:
  temp[(0+3)%7] = temp[3] = 1
  temp[(1+3)%7] = temp[4] = 2
  temp[(2+3)%7] = temp[5] = 3
  temp[(3+3)%7] = temp[6] = 4
  temp[(4+3)%7] = temp[0] = 5
  temp[(5+3)%7] = temp[1] = 6
  temp[(6+3)%7] = temp[2] = 7
  temp = [5,6,7,1,2,3,4] ✓
```

**O(N) time, O(N) space.** Works perfectly. Easy to understand.
But the follow-up asks: can you do it with O(1) extra space?

---

## 💡 Approach 2 — Three Reversals (The Elegant Way)

### 📜 The Scroll of the Three Reversals

The idea: the rotated array is made of two blocks that SWAP positions.

```
Original:  [ A  A  A  A | B  B  B ]
                first 4     last 3

Rotated:   [ B  B  B | A  A  A  A ]
              last 3     first 4
```

Block B (last k elements) moves to the front.
Block A (first n-k elements) moves to the back.

How do three reversals achieve this swap?

```
Original:       [ 1  2  3  4 | 5  6  7 ]
                     A            B

Step 1 — Reverse ALL:
                [ 7  6  5 | 4  3  2  1 ]
                   B_rev      A_rev

Step 2 — Reverse first k:
                [ 5  6  7 | 4  3  2  1 ]
                    B          A_rev

Step 3 — Reverse last n-k:
                [ 5  6  7 | 1  2  3  4 ]
                    B           A
```

**Why this works — the visual proof:**

After the full reversal, B is at the front and A is at the back.
But both are REVERSED internally.

The two partial reversals UN-REVERSE each block,
restoring their internal order while keeping their positions swapped.

> _"Reversing the whole array is like flipping a book upside down.
> The chapters are in the right place — back became front —
> but each chapter's pages are backwards.
> The two partial reversals flip each chapter's pages back to normal."_

---

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔄 The Reverse Helper

```cpp
void reverse(vector<int>& nums, int left, int right) {
    while (left < right) {
        swap(nums[left], nums[right]);
        left++;
        right--;
    }
}
```

Two pointers walk inward, swapping at each step.
O(N) time, O(1) space.

---

## 🔄 The Three-Step Rotation

```cpp
void rotate(vector<int>& nums, int k) {
    int n = nums.size();
    k = k % n;
```

Reduce k to effective rotation.

---

### Step 1 — Reverse the entire array

```cpp
    reverse(nums, 0, n - 1);
```

`[1,2,3,4,5,6,7]` → `[7,6,5,4,3,2,1]`

B is now at the front (reversed). A is at the back (reversed).

---

### Step 2 — Reverse the first k elements

```cpp
    reverse(nums, 0, k - 1);
```

`[7,6,5 | 4,3,2,1]` → `[5,6,7 | 4,3,2,1]`

B is now in correct order.

---

### Step 3 — Reverse the remaining n-k elements

```cpp
    reverse(nums, k, n - 1);
}
```

`[5,6,7 | 4,3,2,1]` → `[5,6,7 | 1,2,3,4]`

A is now in correct order. Rotation complete.

---

### 🎺 The Trial of the Three Reversals

```cpp
int main() {
    vector<int> n1 = {1, 2, 3, 4, 5, 6, 7};
    rotate(n1, 3);
    for (int x : n1) cout << x << " ";
    cout << endl; // expected: 5 6 7 1 2 3 4

    vector<int> n2 = {-1, -100, 3, 99};
    rotate(n2, 2);
    for (int x : n2) cout << x << " ";
    cout << endl; // expected: 3 99 -1 -100

    vector<int> n3 = {1, 2, 3};
    rotate(n3, 5);
    for (int x : n3) cout << x << " ";
    cout << endl; // expected: 2 3 1

    return 0;
}
```

---

**Full visual trace for nums = [1,2,3,4,5,6,7], k = 3:**

```
Goal: last 3 to front → [5,6,7,1,2,3,4]

Original:    1   2   3   4  |  5   6   7
             ───── A ─────    ─── B ───

Step 1 — Reverse ALL:
             7   6   5  |  4   3   2   1
             ── B_rev ──   ──── A_rev ────

Step 2 — Reverse first 3 (un-reverse B):
             5   6   7  |  4   3   2   1
             ─── B ────   ──── A_rev ────

Step 3 — Reverse last 4 (un-reverse A):
             5   6   7  |  1   2   3   4
             ─── B ────   ───── A ──────

Result:  [5, 6, 7, 1, 2, 3, 4] ✓
```

---

**Trace for nums = [-1,-100,3,99], k = 2:**

```
k = 2 % 4 = 2. A = first 2, B = last 2.

Original:    -1  -100  |  3   99

Step 1:      99   3   | -100  -1
Step 2:       3  99   | -100  -1
Step 3:       3  99   |  -1  -100

Result: [3, 99, -1, -100] ✓
```

---

**Trace for nums = [1,2,3], k = 5:**

```
k = 5 % 3 = 2. A = first 1, B = last 2.

Original:    1  |  2   3

Step 1:      3   2  |  1
Step 2:      2   3  |  1
Step 3:      2   3  |  1  (single element, no change)

Result: [2, 3, 1] ✓
```

---

## 🔍 The Two Approaches Compared

| Aspect | Extra Space | Three Reversals |
|--------|------------|-----------------|
| Time | O(N) | O(N) |
| Space | O(N) | **O(1)** |
| Concept | Direct placement | Reversal trick |
| Code | Simpler | Slightly more |
| Follow-up | Doesn't satisfy O(1) | ✓ Satisfies |

Both are O(N) time. The reversal approach wins on space.
Know both — start with extra space if asked, then optimize.

---

## 🔍 Left Rotation vs Right Rotation

This problem rotates RIGHT (last k move to front).

For LEFT rotation (first k move to back):
-   Reverse first k, reverse last n-k, reverse all.
-   Or: right rotate by `n - k`.

Same trick, different order.

---

## 🔍 Edge Cases

**k = 0 or k = n:** `k % n = 0`. No rotation. All reversals cancel out.
**k > n:** `k % n` reduces it. Works correctly.
**n = 1:** single element. Any rotation = no change.

---

### 🧠 Memory of the Three Reversals Law

-   **`k = k % n`** — reduce to effective rotation
-   **Extra space:** `temp[(i+k)%n] = nums[i]`. O(N) time, O(N) space.
-   **Three reversals (optimal):**
    -   Reverse entire array `[0, n-1]`
    -   Reverse first k `[0, k-1]`
    -   Reverse remaining `[k, n-1]`
-   **Why it works:** full reversal swaps blocks A and B but reverses them internally; partial reversals fix the internal order
-   **Left rotation:** reverse first k, reverse last n-k, reverse all
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Rotate Array**,
where the Oracle performed three reversals --
first the whole array flipped end to end
like a book turned upside down,
then each chapter's pages were flipped back to normal --
and the rotation was complete,
in-place, in linear time,
with nothing but two pointers
and the elegance of reversal. 🔄🔁✨
