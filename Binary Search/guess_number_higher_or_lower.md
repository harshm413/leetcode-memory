## 🎲👑 _The Whispering Oracle's Challenge: The Guess Number Higher or Lower Saga_

> \_"In a kingdom of hidden numbers,
> the King chose a secret value between 1 and `n`.
>
> The Oracle was summoned and told:
>
> **'You may guess the number.
> After each guess, I will whisper one of three responses:
>
> -1 means my number is LOWER than your guess.
>  1 means my number is HIGHER than your guess.
>  0 means you have found it.'**
>
> The Oracle could not guess blindly --
> trying every number from 1 to n would take O(N) time.
>
> Instead, she trusted the ancient ritual of halving.
>
> **Start with the full range [1, n].
> Guess the middle.
> The King's whisper tells you which half to keep.
> Halve the range. Guess again.
> Repeat until the secret stands alone.**
>
> Each whisper eliminated half the possibilities.
> After log2(N) guesses, the secret was found.
>
> This was binary search in its purest form --
> not searching an array,
> but searching a range of numbers
> guided by an oracle's feedback."\_

---

This is the saga of **Guess Number Higher or Lower**.

You are playing a guessing game. The rules:

-   I pick a number from `1` to `n`.
-   You guess a number. I tell you if my number is higher, lower, or equal.
-   You call a pre-defined API `guess(int num)` which returns:
    -   `-1` if the secret is **lower** than `num` (your guess is too high)
    -   `1` if the secret is **higher** than `num` (your guess is too low)
    -   `0` if you guessed correctly

Find the secret number.

```
Input:  n = 10, secret = 6
Output: 6

Input:  n = 1, secret = 1
Output: 1

Input:  n = 2, secret = 1
Output: 1
```

---

## 🧠 The Oracle's Core Insight -- Binary Search on a Range

This is the textbook application of binary search --
not on an array, but on the integer range `[1, n]`.

The `guess()` API acts as the comparator:

```
guess(mid) == 0   -->  found it, return mid
guess(mid) == -1  -->  secret < mid, search left half
guess(mid) == 1   -->  secret > mid, search right half
```

Each call to `guess()` eliminates half the remaining range.

```
Time:  O(log N) -- halving at every step
Space: O(1) -- only two boundary variables
```

---

### 📜 The Scroll of the Hidden Number

```cpp
#include <iostream>
using namespace std;

// Forward declaration of the guess API
// (provided by the problem, not implemented by us)
int guess(int num);
```

---

## ⚔️ The Oracle's Guessing Ritual

```cpp
int guessNumber(int n) {
    int left = 1;
    int right = n;
```

Two sentinels were placed at the boundaries of the unknown:

-   `left = 1` -- the smallest possible secret.
-   `right = n` -- the largest possible secret.

The secret lay somewhere between them.

---

## 🔁 Halve Until Found

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Oracle guessed the middle of the current range.

`left + (right - left) / 2` prevented integer overflow --
critical when `n` is close to `INT_MAX`.

---

### 🎯 Ask the King

```cpp
        int res = guess(mid);
```

The King's whisper was received.

---

### ✅ Whisper: "You Found It"

```cpp
        if (res == 0) {
            return mid;
        }
```

The guess was correct. The secret was `mid`.
The quest ended.

---

### ⬅️ Whisper: "My Number Is Lower"

```cpp
        else if (res == -1) {
            right = mid - 1;
        }
```

The secret was smaller than `mid`.
Everything at `mid` and above was eliminated.

The right sentinel retreated: `right = mid - 1`.

---

### ➡️ Whisper: "My Number Is Higher"

```cpp
        else {
            left = mid + 1;
        }
    }
```

The secret was larger than `mid`.
Everything at `mid` and below was eliminated.

The left sentinel advanced: `left = mid + 1`.

---

## 🏁 Safety Return

```cpp
    return -1;
}
```

This line should never be reached --
the problem guarantees a valid secret exists in `[1, n]`.
It's a safety net for the compiler.

---

### 🎺 The Trial of the Hidden Number

```cpp
int main() {
    // Assume secret = 6, n = 10
    cout << guessNumber(10) << endl; // expected: 6
    return 0;
}
```

---

**Full trace for n = 10, secret = 6:**

| Step | left | right | mid | guess(mid) | Meaning          | Action       |
| ---- | ---- | ----- | --- | ---------- | ---------------- | ------------ |
| 1    | 1    | 10    | 5   | 1          | Secret > 5       | left = 6     |
| 2    | 6    | 10    | 8   | -1         | Secret < 8       | right = 7    |
| 3    | 6    | 7     | 6   | 0          | Secret == 6      | Return **6** |

**Answer: 6** ✓

Three guesses to find the secret among 10 numbers.
log2(10) ~ 3.3, so 3 guesses is expected.

---

**Full trace for n = 2, secret = 1:**

| Step | left | right | mid | guess(mid) | Meaning          | Action       |
| ---- | ---- | ----- | --- | ---------- | ---------------- | ------------ |
| 1    | 1    | 2     | 1   | 0          | Secret == 1      | Return **1** |

**Answer: 1** ✓

Found on the first guess -- mid happened to be the secret.

---

**Full trace for n = 100, secret = 73:**

| Step | left | right | mid | guess(mid) | Action       |
| ---- | ---- | ----- | --- | ---------- | ------------ |
| 1    | 1    | 100   | 50  | 1          | left = 51    |
| 2    | 51   | 100   | 75  | -1         | right = 74   |
| 3    | 51   | 74    | 62  | 1          | left = 63    |
| 4    | 63   | 74    | 68  | 1          | left = 69    |
| 5    | 69   | 74    | 71  | 1          | left = 72    |
| 6    | 72   | 74    | 73  | 0          | Return **73**|

**Answer: 73** ✓

Six guesses to find the secret among 100 numbers.
log2(100) ~ 6.6, so 6-7 guesses is expected.

---

## 🔍 Why This Is Pure Binary Search

This problem strips binary search to its essence:

-   No array to index into.
-   No sorted data structure.
-   Just a range `[1, n]` and a comparison oracle.

The `guess()` API is the comparator.
The range `[left, right]` is the search space.
The algorithm is identical to searching a sorted array --
but the "array" is the sequence of integers 1 to n,
and the "comparison" is the King's whisper.

Every binary search problem -- no matter how complex --
reduces to this same skeleton:
define a search space, define a comparator, halve until found.

---

### 🧠 Memory of the Whispering Law

-   **Search space:** `[1, n]` -- the range of possible secrets
-   **Comparator:** `guess(mid)` -- returns -1, 0, or 1
-   **Loop:** `while (left <= right)` -- search space has at least one candidate
-   **Midpoint:** `left + (right - left) / 2` -- overflow-safe
-   **Three cases:**
    -   `guess(mid) == 0` --> found, return `mid`
    -   `guess(mid) == -1` --> secret < mid, `right = mid - 1`
    -   `guess(mid) == 1` --> secret > mid, `left = mid + 1`
-   **Time:** O(log N) -- halving at every step
-   **Space:** O(1) -- only two boundary variables

Thus is remembered the saga of **Guess Number Higher or Lower**,
where the Oracle did not guess wildly
but listened carefully to each whisper from the King --
halving the unknown with every response --
until the secret number stood revealed
in the narrowing silence between two converging sentinels. 🎲👑✨
