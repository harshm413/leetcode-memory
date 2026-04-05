## 🌳🔢 _The Root Beneath the Mountain: The Sqrt(x) Saga_

> \_"In the Kingdom of Perfect Squares,
> every number cast a shadow --
> a root buried deep beneath it.
>
> The Oracle was summoned before the King:
>
> **'Given a number x,
> find the largest integer whose square
> does not exceed x.
> This is the integer square root --
> the floor of the true root.'**
>
> The Oracle could not compute floating point --
> the decree demanded integers only.
>
> She realized this was not a math problem.
> It was a **search problem**.
>
> The answer lay somewhere in the range `[0, x]`.
> For any candidate `mid`:
>
> **If mid * mid <= x -- mid might be the answer. Try larger.**
> **If mid * mid > x -- mid is too big. Try smaller.**
>
> Binary search on the answer space.
> The same pattern as Koko's bananas
> and the shipping capacity --
> but this time, the answer was a square root."\_

---

This is the saga of **Sqrt(x)**.

Given a non-negative integer `x`,
return the **integer square root** of `x` --
the largest integer `r` such that `r * r <= x`.

Truncate the decimal part (floor, not round).

```
Input:  x = 4   →  Output: 2   (sqrt(4) = 2.0)
Input:  x = 8   →  Output: 2   (sqrt(8) = 2.828..., floor = 2)
Input:  x = 0   →  Output: 0
Input:  x = 1   →  Output: 1
Input:  x = 16  →  Output: 4
```

---

## 🧠 The Oracle's Core Insight -- Binary Search on the Answer

The integer square root of `x` is the largest integer `r`
where `r * r <= x`.

The search space is `[0, x]`.
But we can tighten it to `[0, x/2 + 1]` since for `x >= 2`,
`sqrt(x) <= x/2`.

For any candidate `mid`:

```
If mid * mid <= x  -->  mid is valid, but a larger root might exist
                        Record mid as answer, search right: left = mid + 1

If mid * mid > x   -->  mid is too large
                        Search left: right = mid - 1
```

This is the classic **binary search on answer with answer tracking** --
the same pattern used in Koko Eating Bananas and Ship Capacity.

**Overflow warning:** `mid * mid` can overflow 32-bit integers.
Use `long long` for the multiplication.

```
Time:  O(log x) -- halving the range at every step
Space: O(1) -- only a few variables
```

---

### 📜 The Scroll of the Hidden Root

```cpp
#include <iostream>
using namespace std;
```

---

## ⚔️ The Oracle's Root-Finding Ritual

```cpp
int mySqrt(int x) {
    if (x < 2) return x;
```

The Oracle handled the trivial cases first.

`sqrt(0) = 0` and `sqrt(1) = 1` --
both numbers are their own square roots.
No search needed.

---

## 🧭 Define the Search Space

```cpp
    int left = 1;
    int right = x / 2;
    int answer = 1;
```

For `x >= 2`, the square root is at most `x / 2`.

Why? Because `(x/2)² >= x` for all `x >= 4`,
and we already handled `x < 2`.

`answer` was initialized to `1` --
a safe default since `sqrt(x) >= 1` for `x >= 1`.

---

## 🔁 Halve Until the Root Is Found

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
        long long square = (long long)mid * mid;
```

The Oracle tested the middle candidate.

`(long long)mid * mid` prevented integer overflow --
critical when `x` is close to `INT_MAX` (2,147,483,647).
Without the cast, `mid * mid` could exceed 32-bit range.

---

### ✅ Square Fits -- Record and Try Larger

```cpp
        if (square <= x) {
            answer = mid;
            left = mid + 1;
        }
```

`mid * mid <= x` -- this candidate was valid.

The Oracle recorded `mid` as the best answer so far,
then pushed `left` rightward to search for a larger valid root.

> _"A valid root is good.
> But a larger valid root is better.
> The Oracle always hunts for the maximum."_

Note: when `square == x`, we have found the exact root.
But we don't need a special case --
recording it as `answer` and letting the loop finish
naturally produces the correct result.

---

### ❌ Square Too Large -- Try Smaller

```cpp
        else {
            right = mid - 1;
        }
    }
```

`mid * mid > x` -- this candidate was too large.
The Oracle retreated: `right = mid - 1`.

---

## 🌳 The Root Is Revealed

```cpp
    return answer;
}
```

When the loop ended, `answer` held the largest integer
whose square did not exceed `x` --
the integer square root.

---

### 🎺 The Trial of the Hidden Roots

```cpp
int main() {
    cout << mySqrt(4)  << endl; // expected: 2
    cout << mySqrt(8)  << endl; // expected: 2
    cout << mySqrt(0)  << endl; // expected: 0
    cout << mySqrt(1)  << endl; // expected: 1
    cout << mySqrt(16) << endl; // expected: 4
    cout << mySqrt(2147483647) << endl; // expected: 46340
    return 0;
}
```

---

**Full trace for x = 8:**

Search range: `left = 1`, `right = 4`, `answer = 1`.

| Step | left | right | mid | mid² | mid² <= 8? | Action            |
| ---- | ---- | ----- | --- | ---- | ---------- | ----------------- |
| 1    | 1    | 4     | 2   | 4    | Yes        | ans=2, left=3     |
| 2    | 3    | 4     | 3   | 9    | No         | right=2           |
| 3    | 3    | 2     | --  | --   | left>right | Exit loop         |

**Answer: 2** ✓

`2² = 4 <= 8` ✓, `3² = 9 > 8` ✗. So floor(sqrt(8)) = 2.

---

**Full trace for x = 16:**

Search range: `left = 1`, `right = 8`, `answer = 1`.

| Step | left | right | mid | mid² | mid² <= 16? | Action            |
| ---- | ---- | ----- | --- | ---- | ----------- | ----------------- |
| 1    | 1    | 8     | 4   | 16   | Yes         | ans=4, left=5     |
| 2    | 5    | 8     | 6   | 36   | No          | right=5           |
| 3    | 5    | 5     | 5   | 25   | No          | right=4           |
| 4    | 5    | 4     | --  | --   | left>right  | Exit loop         |

**Answer: 4** ✓

Found the exact root at step 1. The loop continued to confirm
no larger root existed, then exited.

---

**Full trace for x = 4:**

Search range: `left = 1`, `right = 2`, `answer = 1`.

| Step | left | right | mid | mid² | mid² <= 4? | Action            |
| ---- | ---- | ----- | --- | ---- | ---------- | ----------------- |
| 1    | 1    | 2     | 1   | 1    | Yes        | ans=1, left=2     |
| 2    | 2    | 2     | 2   | 4    | Yes        | ans=2, left=3     |
| 3    | 3    | 2     | --  | --   | left>right | Exit loop         |

**Answer: 2** ✓

---

**Edge case: x = 2147483647 (INT_MAX):**

`sqrt(2147483647) ≈ 46340.95` → floor = **46340**.

Without `long long`, `46341 * 46341 = 2,147,488,281` overflows 32-bit int.
The `(long long)mid * mid` cast prevents this silently lethal bug.

`46340² = 2,147,395,600 <= 2,147,483,647` ✓
`46341² = 2,147,488,281 > 2,147,483,647` ✗

**Answer: 46340** ✓

---

## ⚠️ The Overflow Trap -- Why `long long` Is Essential

The most dangerous pitfall in this problem:

```cpp
// WRONG -- overflows for large x
if (mid * mid <= x)

// CORRECT -- safe for all x up to INT_MAX
if ((long long)mid * mid <= x)
```

When `mid = 46341` and `x = 2147483647`:
-   `mid * mid` as `int` = **-2147479015** (overflow, wraps negative)
-   `mid * mid` as `long long` = **2147488281** (correct)

The overflow would cause the condition to be `true` when it should be `false`,
producing a wrong answer. Always use `long long` for the square.

---

### 🧠 Memory of the Hidden Root Law

-   **Binary search on the answer** -- search `[1, x/2]` for the largest `r` where `r² <= x`
-   **Base case:** `x < 2` → return `x` directly
-   **Overflow protection:** `(long long)mid * mid` -- critical for large `x`
-   **Binary search logic (left <= right with answer tracking):**
    -   If `mid² <= x` → `answer = mid`, `left = mid + 1` (try larger)
    -   If `mid² > x` → `right = mid - 1` (too large)
-   **Return `answer`** -- the largest valid root found
-   No special case needed for exact squares -- they are naturally captured
-   **Time:** O(log x) -- halving at every step
-   **Space:** O(1) -- only a few variables
-   **Edge cases:**
    -   `x = 0` → return 0
    -   `x = 1` → return 1
    -   `x = INT_MAX` → works correctly with `long long` guard

Thus is remembered the saga of **Sqrt(x)**,
where the Oracle did not compute the root with floating point
but instead searched the integer range with binary halving --
testing each candidate's square against the mountain,
recording every valid root and pushing for a larger one --
until the search space collapsed
and the deepest root beneath the mountain
was unearthed in logarithmic time,
guarded always by the `long long` shield
against the silent treachery of overflow. 🌳🔢✨
