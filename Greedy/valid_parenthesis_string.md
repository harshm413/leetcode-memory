## ✅🌟 _The Wildcard Brackets: The Valid Parenthesis String Saga_

> \_"The Oracle was given a string containing
> three types of characters:
> -   `'('` -- an opening bracket.
> -   `')'` -- a closing bracket.
> -   `'*'` -- a **wildcard** that could be `(`, `)`, or EMPTY.
>
> She was commanded:
>
> **'Is this string a valid parentheses string?'**
>
> Without wildcards, this is a simple counter problem.
> But `*` could be anything -- making it ambiguous.
>
> The Oracle devised a greedy approach:
>
> **Track a RANGE of possible open-bracket counts.**
>
> Instead of one counter, maintain TWO:
> -   `low` -- the MINIMUM possible open count
>     (treat every `*` as `)` or empty).
> -   `high` -- the MAXIMUM possible open count
>     (treat every `*` as `(`).
>
> At every character:
> -   `(` → both low and high increase by 1.
> -   `)` → both low and high decrease by 1.
> -   `*` → low decreases by 1 (could be `)`) AND high increases by 1 (could be `(`).
>
> **If `high` ever goes negative** → too many `)` even in the best case → invalid.
> **If `low` goes negative** → clamp it to 0 (we'd choose `*` as empty instead).
> **At the end, if `low == 0`** → there exists an assignment where all brackets match → valid."\_

---

This is the saga of **Valid Parenthesis String** (LeetCode 678).

Given a string `s` containing `(`, `)`, and `*`:

-   `*` can be treated as `(`, `)`, or an empty string.
-   Return `true` if the string can be valid.

```
Input:  s = "()"
Output: true

Input:  s = "(*)"
Output: true   (* treated as empty or as matching bracket)

Input:  s = "(*))"
Output: true   (* treated as '(')

Input:  s = ")("
Output: false
```

---

## 🧠 The Oracle's Core Insight -- Range of Possible Open Counts

Instead of trying all 3^n possibilities for `*` (exponential),
track the **range** `[low, high]` of possible unmatched `(` counts.

At any point, the actual number of unmatched `(` could be
anywhere in `[low, high]`. If this range includes 0 at the end → valid.

**Three rules per character:**

| Character | Effect on `low` | Effect on `high` |
|-----------|-----------------|------------------|
| `(`       | +1              | +1               |
| `)`       | -1              | -1               |
| `*`       | -1 (could be `)`)| +1 (could be `(`) |

**Two invariants:**
-   If `high < 0` → impossible. Even treating all `*` as `(`, too many `)`. Return false.
-   If `low < 0` → clamp to 0. We'd choose `*` as empty (not `)`) to avoid going negative.

**Final check:** if `low == 0` → there exists a valid assignment → true.

```
Time:  O(n) -- single pass
Space: O(1) -- two variables
```

---

### 📜 The Scroll of the Wildcard Brackets

```cpp
#include <iostream>
#include <string>
using namespace std;
```

---

## 🌟 The Greedy Ritual

```cpp
bool checkValidString(string s) {
    int low = 0;
    int high = 0;
```

`low` = minimum possible unmatched `(` count.
`high` = maximum possible unmatched `(` count.

Both start at 0 (no brackets seen yet).

---

### 🔁 Process Each Character

```cpp
    for (char c : s) {
```

---

### `(` -- Definitely Opens

```cpp
        if (c == '(') {
            low++;
            high++;
        }
```

An opening bracket increases both the minimum and maximum
possible open counts by 1. No ambiguity.

---

### `)` -- Definitely Closes

```cpp
        else if (c == ')') {
            low--;
            high--;
        }
```

A closing bracket decreases both by 1.
It consumes one unmatched `(` in every scenario.

---

### `*` -- Could Be Anything

```cpp
        else {
            low--;
            high++;
        }
```

The wildcard expands the range:
-   In the BEST case (for opening), it's `(` → high increases.
-   In the WORST case (for opening), it's `)` → low decreases.
-   (It could also be empty, which is between the two.)

> _"The wildcard stretches the range of possibilities.
> It could open a new bracket (high grows)
> or close an existing one (low shrinks)
> or do nothing at all."_

---

### 🛡️ Clamp Low to 0

```cpp
        if (low < 0) low = 0;
```

If `low` goes negative, it means we'd need `*` to be `)` more times
than there are unmatched `(`. That's not possible --
we'd just treat those `*` as empty instead.

Clamping to 0 means: "the minimum possible open count is 0,
not negative. We'd choose `*` as empty to avoid going below 0."

> _"The count of unmatched opens can never truly be negative.
> If the minimum tries to go below zero,
> we simply choose the wildcard as empty --
> keeping the count at zero."_

---

### 🛑 High Goes Negative -- Impossible

```cpp
        if (high < 0) return false;
    }
```

If `high < 0`, even in the BEST case (treating all `*` as `(`),
there are still too many unmatched `)`. Impossible. Return false.

> _"If even the most optimistic scenario fails --
> if even treating every wildcard as an opening bracket
> cannot save us from excess closings --
> then the string is broken beyond repair."_

---

### 🏁 Final Check

```cpp
    return low == 0;
}
```

At the end, `low == 0` means:
there EXISTS an assignment of wildcards
where all brackets are perfectly matched (0 unmatched opens).

If `low > 0`, even in the best case (treating `*` as `)`),
there are still unmatched `(`. Invalid.

Since we clamped `low` to never go below 0,
`low == 0` is the only valid final state.

---

### 🎺 The Trial of the Wildcard Brackets

```cpp
int main() {
    cout << checkValidString("()") << endl;    // expected: 1 (true)
    cout << checkValidString("(*)") << endl;   // expected: 1 (true)
    cout << checkValidString("(*))") << endl;  // expected: 1 (true)
    cout << checkValidString(")(") << endl;    // expected: 0 (false)
    cout << checkValidString("(***)") << endl; // expected: 1 (true)
    cout << checkValidString("(((") << endl;   // expected: 0 (false)
    return 0;
}
```

---

**Full trace for s = "(*)":**

| i | char | low before | high before | Action        | low after | high after |
|---|------|-----------|-------------|---------------|-----------|------------|
| 0 | `(`  | 0         | 0           | +1, +1        | 1         | 1          |
| 1 | `*`  | 1         | 1           | -1, +1        | 0         | 2          |
| 2 | `)`  | 0         | 2           | -1, -1        | -1→**0**  | 1          |

Final: `low == 0`. **Answer: true** ✓

The `*` could be `)` → `()()` wait, let me think...
Actually `(*)` with `*` as empty = `()`. Valid. ✓

---

**Full trace for s = "(*))":**

| i | char | low | high | After         |
|---|------|-----|------|---------------|
| 0 | `(`  | 0→1| 0→1  | low=1, high=1 |
| 1 | `*`  | 1→0| 1→2  | low=0, high=2 |
| 2 | `)`  | 0→-1→**0** | 2→1 | low=0, high=1 |
| 3 | `)`  | 0→-1→**0** | 1→0 | low=0, high=0 |

Final: `low == 0`. **Answer: true** ✓

Assignment: `*` = `(` → string becomes `(())`. Valid.

---

**Full trace for s = ")(":**

| i | char | low | high | After         |
|---|------|-----|------|---------------|
| 0 | `)`  | 0→-1→**0** | 0→-1 | high < 0 → **return false** |

**Answer: false** ✓

Even treating all future `*` as `(`, the first `)` has nothing to match.

---

**Trace for s = "(((":**

| i | char | low | high |
|---|------|-----|------|
| 0 | `(`  | 1   | 1    |
| 1 | `(`  | 2   | 2    |
| 2 | `(`  | 3   | 3    |

Final: `low == 3 ≠ 0`. **Answer: false** ✓

Three unmatched opens. No wildcards to close them.

---

**Trace for s = "***":**

| i | char | low | high |
|---|------|-----|------|
| 0 | `*`  | -1→0 | 1  |
| 1 | `*`  | -1→0 | 2  |
| 2 | `*`  | -1→0 | 3  |

Final: `low == 0`. **Answer: true** ✓

All wildcards treated as empty → `""`. Valid (empty string is valid).

---

## 🔍 Why Two Variables Instead of One Counter

With just `(` and `)`, one counter suffices:
increment for `(`, decrement for `)`, check ≥ 0 always and == 0 at end.

With `*`, one counter can't capture the ambiguity.
The `*` could be any of three things -- the actual count
could be anywhere in a RANGE.

`[low, high]` captures all possible states simultaneously.
If 0 is within this range at the end → some valid assignment exists.

---

## 🔍 Why Clamp Low (Not High)

`low < 0` means: "in the worst case, we'd have negative opens."
But that's not a real state -- we'd just choose `*` as empty.
So we clamp to 0 (the real minimum).

`high < 0` means: "even in the BEST case, we have negative opens."
That's a real impossibility. No clamping -- return false immediately.

---

### 🧠 Memory of the Wildcard Brackets Law

-   **Two variables:** `low` (min possible opens), `high` (max possible opens)
-   **`(`:** low++, high++
-   **`)`:** low--, high--
-   **`*`:** low-- (could be `)`), high++ (could be `(`)
-   **Clamp:** if `low < 0` → set to 0 (choose `*` as empty)
-   **Impossible:** if `high < 0` → return false (too many `)` even optimistically)
-   **Valid:** if `low == 0` at the end → some assignment works
-   **Time:** O(n). **Space:** O(1).
-   **Edge cases:**
    -   No wildcards → simple counter check
    -   All wildcards → always valid (treat all as empty)
    -   Starts with `)` → immediately false (high goes negative)

Thus is remembered the saga of **Valid Parenthesis String**,
where the Oracle did not try every possible assignment of wildcards
but instead tracked the RANGE of possible open counts --
low for the pessimistic case, high for the optimistic --
clamping the low when it dipped below zero,
failing when even the high couldn't stay afloat --
and at the end, if zero lived within the range,
the string was declared valid. ✅🌟✨
