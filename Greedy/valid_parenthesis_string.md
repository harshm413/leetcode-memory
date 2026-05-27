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

## 🔄 DP Approach -- Recursion + Memoization

Before the greedy insight, this can be solved with recursion.

`solve(i, open)` = can the string from index `i` onward be valid,
given that there are `open` unmatched `(` so far?

### Raw Recursion

```cpp
bool solve(int i, int open, string& s) {
    if (open < 0) return false;
    if (i == (int)s.size()) return open == 0;
```

If `open < 0` → more `)` than `(` → invalid.
If we've reached the end → valid only if `open == 0` (all matched).

---

```cpp
    if (s[i] == '(') {
        return solve(i + 1, open + 1, s);
    } else if (s[i] == ')') {
        return solve(i + 1, open - 1, s);
    } else {
        // '*' can be '(', ')', or empty
        return solve(i + 1, open + 1, s)    // treat as '('
            || solve(i + 1, open - 1, s)    // treat as ')'
            || solve(i + 1, open, s);       // treat as empty
    }
}

bool checkValidString(string s) {
    return solve(0, 0, s);
}
```

For `*`: try all three options. If ANY returns true → valid.

```
Raw Recursion:
Time:  O(3^n) -- three branches per '*'
Space: O(n) -- recursion stack
```

---

### Memoization

The state is `(i, open)`. Cache it.

```cpp
bool solve(int i, int open, string& s, vector<vector<int>>& memo) {
    if (open < 0) return false;
    if (i == (int)s.size()) return open == 0;
    if (memo[i][open] != -1) return memo[i][open];
```

---

```cpp
    bool result;
    if (s[i] == '(') {
        result = solve(i + 1, open + 1, s, memo);
    } else if (s[i] == ')') {
        result = solve(i + 1, open - 1, s, memo);
    } else {
        result = solve(i + 1, open + 1, s, memo)
              || solve(i + 1, open - 1, s, memo)
              || solve(i + 1, open, s, memo);
    }
    return memo[i][open] = result;
}
```

---

```cpp
bool checkValidString(string s) {
    int n = s.size();
    vector<vector<int>> memo(n, vector<int>(n + 1, -1));
    return solve(0, 0, s, memo);
}
```

`memo[i][open]` = can we make the string valid from index `i` with `open` unmatched `(`?
`open` ranges from 0 to n (at most n unmatched opens possible).

```
Memoization:
Time:  O(n²) -- n indices × n possible open counts
Space: O(n²) -- memo table + recursion stack
```

---

### Why Greedy Is Better

| Approach       | Time   | Space  |
| -------------- | ------ | ------ |
| Raw Recursion  | O(3ⁿ)  | O(n)   |
| Memoization    | O(n²)  | O(n²)  |
| **Greedy**     | **O(n)** | **O(1)** |

The greedy approach collapses the entire `[0, n]` range of possible open counts
into just two variables (`low`, `high`). Same information, no table needed.

**For interviews:** mention recursion → memo → greedy as the optimization path.

---

## 🔍 Why Clamp Low (Not High)

`low < 0` means: "in the worst case, we'd have negative opens."
But that's not a real state -- we'd just choose `*` as empty.
So we clamp to 0 (the real minimum).

`high < 0` means: "even in the BEST case, we have negative opens."
That's a real impossibility. No clamping -- return false immediately.

---

## 🔍 What Do `low` and `high` Actually Mean at Any Moment?

Think of it this way: at any point in the string,
the ACTUAL number of unmatched `(` depends on how we assign the `*`s.

`low` = the FEWEST unmatched `(` we could have right now
(if we treated every `*` seen so far as `)` or empty).

`high` = the MOST unmatched `(` we could have right now
(if we treated every `*` seen so far as `(`).

The real count is SOMEWHERE in `[low, high]`.

**Example: s = "(*"**

| After | low | high | Meaning |
|-------|-----|------|---------|
| `(`   | 1   | 1    | Exactly 1 unmatched `(`. No ambiguity yet. |
| `(*`  | 0   | 2    | If `*` = `)` → 0 unmatched. If `*` = `(` → 2 unmatched. If `*` = empty → 1 unmatched. Real count is 0, 1, or 2. |

So `[low=0, high=2]` means: "depending on how we assign `*`,
we could have 0, 1, or 2 unmatched opens right now."

**Example: s = "((**"**

| After  | low | high | Meaning |
|--------|-----|------|---------|
| `(`    | 1   | 1    | 1 open. |
| `((`   | 2   | 2    | 2 opens. |
| `((*`  | 1   | 3    | `*`=`)` → 1 open. `*`=empty → 2. `*`=`(` → 3. Range [1,3]. |
| `((**` | 0   | 4    | Second `*` expands range further. Could be 0 to 4 opens. |

At the end: `low = 0`. Since 0 is in the range → there EXISTS an assignment
where all brackets match. Valid!

(Assignment: both `*` = `)` → `(())` → valid.)

**Example: s = ")*"**

| After | low | high | Meaning |
|-------|-----|------|---------|
| `)`   | -1→**0** | -1 | high < 0 → **IMPOSSIBLE**. Return false. |

Even if we treat the `*` as `(` later, the `)` at position 0
has NOTHING to match with (no `(` before it). Dead immediately.

**Example: s = "*()"**

| After | low | high | Meaning |
|-------|-----|------|---------|
| `*`   | -1→**0** | 1 | `*`=`)` would give -1 (clamped to 0). `*`=`(` gives 1. Range [0,1]. |
| `*(`  | 1   | 2    | The `(` pushes both up. Range [1,2]. |
| `*()` | 0   | 1    | The `)` pulls both down. Range [0,1]. |

End: `low = 0` → valid! (Assignment: `*` = empty → `()` → valid.)

**The key mental model:**

> `[low, high]` is the RANGE of possible unmatched-open-counts
> across ALL possible assignments of the `*`s seen so far.
> If 0 is ever in this range at the end → some assignment works → valid.
> If high goes negative → even the best assignment fails → invalid.
> If low goes negative → clamp it (we'd just choose `*` differently).

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
