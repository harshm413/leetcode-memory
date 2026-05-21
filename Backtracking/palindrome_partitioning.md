## 🪞✂️ _The Mirror Cuts: The Palindrome Partitioning Saga_

> \_"The Oracle was given a string `s`.
>
> She was commanded:
>
> **'Partition the string such that every substring
> in the partition is a PALINDROME.
> Return ALL such possible partitions.'**
>
> For `"aab"`:
> -   `["a", "a", "b"]` -- each part is a palindrome. ✓
> -   `["aa", "b"]` -- "aa" is a palindrome, "b" is a palindrome. ✓
> -   `["aab"]` -- "aab" is NOT a palindrome. ✗
>
> The Oracle saw this as a **front-partitioning** backtracking:
>
> **Stand at position `start`.
> Try every possible prefix starting at `start`.
> If the prefix is a palindrome -- take it as one partition.
> Recurse on the remaining string.
> When `start` reaches the end -- one valid partition is complete.**
>
> At each position, try every prefix length.
> Only proceed if the prefix is a palindrome.
> This naturally prunes invalid partitions."\_

---

This is the saga of **Palindrome Partitioning**.

Given a string `s`:

-   Partition `s` such that every substring is a palindrome.
-   Return all possible palindrome partitions.

```
Input:  s = "aab"
Output: [["a","a","b"], ["aa","b"]]

Input:  s = "a"
Output: [["a"]]

Input:  s = "aba"
Output: [["a","b","a"], ["aba"]]
```

---

## 🧠 The Oracle's Core Insight -- Front Partitioning

This is similar to **Word Break** but instead of checking dictionary membership,
we check if the prefix is a **palindrome**.

And instead of returning true/false, we collect ALL valid partitions.

```
backtrack(start, current):
  If start == n: add current to result. Return.

  For end = start+1 to n:
    prefix = s[start..end-1]
    If prefix is a palindrome:
      current.push(prefix)
      backtrack(end, current)
      current.pop()
```

**At position `start`:** try every possible first cut.
The first piece is `s[start..end-1]`.
If it's a palindrome → take it, recurse on the rest.
If not → skip (prune).

**Base case:** `start == n` → entire string has been partitioned.
Every piece was a palindrome. Record this partition.

```
Time:  O(2ⁿ × n) -- at most 2^(n-1) partitions, O(n) palindrome check each
Space: O(n) -- recursion depth
```

---

### 📜 The Scroll of the Mirror Cuts

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

---

## 🪞 The Palindrome Check

```cpp
bool isPalindrome(string& s, int left, int right) {
    while (left < right) {
        if (s[left] != s[right]) return false;
        left++;
        right--;
    }
    return true;
}
```

Two pointers from both ends. If all characters match → palindrome.
O(n) per check.

---

## ✂️ The Backtracking Ritual

```cpp
void backtrack(string& s, int start,
               vector<string>& current, vector<vector<string>>& result) {
```

The Oracle carried:
-   `s` -- the string to partition.
-   `start` -- the current position (beginning of the unpartitioned part).
-   `current` -- the partition being built (list of palindrome pieces).
-   `result` -- all valid partitions found.

---

### 🎯 Base Case -- Entire String Partitioned

```cpp
    if (start == (int)s.size()) {
        result.push_back(current);
        return;
    }
```

We've reached the end. Every piece in `current` is a palindrome.
This is one valid partition. Record it.

> _"The string is fully consumed.
> Every piece cut so far was a mirror.
> This partition is complete."_

---

### 🔁 Try Every Possible First Piece

```cpp
    for (int end = start; end < (int)s.size(); end++) {
```

`end` is the last index of the current piece.
The piece is `s[start..end]` (inclusive).

Try every possible length for the first piece:
length 1 (`end = start`), length 2 (`end = start+1`), ..., full remaining string.

---

### 🪞 Only Proceed If the Piece Is a Palindrome

```cpp
        if (isPalindrome(s, start, end)) {
```

If `s[start..end]` is NOT a palindrome → skip.
No point partitioning further -- this piece is invalid.

This is the **pruning**. Only palindromic prefixes are explored.

> _"Only mirrors may pass.
> If this piece is not a palindrome,
> the path is abandoned.
> No partition can be built from a broken mirror."_

---

### ✂️ Take This Piece, Recurse on the Rest

```cpp
            current.push_back(s.substr(start, end - start + 1));
            backtrack(s, end + 1, current, result);
            current.pop_back();
        }
    }
}
```

Take the palindromic piece. Add it to the current partition.
Recurse from `end + 1` (the remaining unpartitioned string).
Backtrack -- remove the piece, try a longer one.

---

## 🔮 The Main Function

```cpp
vector<vector<string>> partition(string s) {
    vector<vector<string>> result;
    vector<string> current;
    backtrack(s, 0, current, result);
    return result;
}
```

Start from position 0 with an empty partition.

---

### 🎺 The Trial of the Mirror Cuts

```cpp
int main() {
    auto r1 = partition("aab");
    for (auto& p : r1) {
        for (auto& piece : p) cout << piece << " ";
        cout << endl;
    }
    // expected:
    // a a b
    // aa b

    auto r2 = partition("aba");
    for (auto& p : r2) {
        for (auto& piece : p) cout << piece << " ";
        cout << endl;
    }
    // expected:
    // a b a
    // aba

    return 0;
}
```

---

**Full recursion trace for s = "aab":**

```
backtrack(start=0, current=[])
  end=0: s[0..0]="a". Palindrome? ✓. Push "a". current=["a"].
    backtrack(start=1, current=["a"])
      end=1: s[1..1]="a". Palindrome? ✓. Push "a". current=["a","a"].
        backtrack(start=2, current=["a","a"])
          end=2: s[2..2]="b". Palindrome? ✓. Push "b". current=["a","a","b"].
            backtrack(start=3, current=["a","a","b"])
              → start == n! ADD ["a","a","b"]. ✓
            pop "b".
        pop "a".
      end=2: s[1..2]="ab". Palindrome? ✗. SKIP.
    pop "a".
  end=1: s[0..1]="aa". Palindrome? ✓. Push "aa". current=["aa"].
    backtrack(start=2, current=["aa"])
      end=2: s[2..2]="b". Palindrome? ✓. Push "b". current=["aa","b"].
        backtrack(start=3, current=["aa","b"])
          → start == n! ADD ["aa","b"]. ✓
        pop "b".
    pop "aa".
  end=2: s[0..2]="aab". Palindrome? ✗. SKIP.
```

**Result: [["a","a","b"], ["aa","b"]]** ✓

**Key moments:**
-   `"ab"` is not a palindrome → that branch is pruned entirely.
-   `"aab"` is not a palindrome → pruned.
-   Only palindromic prefixes lead to recursive exploration.

---

**Trace for s = "aba":**

```
backtrack(start=0, current=[])
  end=0: "a" palindrome ✓ → recurse.
    end=1: "b" palindrome ✓ → recurse.
      end=2: "a" palindrome ✓ → ADD ["a","b","a"]. ✓
    end=2: "ba" palindrome? ✗. SKIP.
  end=1: "ab" palindrome? ✗. SKIP.
  end=2: "aba" palindrome? ✓ → recurse.
    start=3 == n → ADD ["aba"]. ✓
```

**Result: [["a","b","a"], ["aba"]]** ✓

---

**Trace for s = "aaa":**

```
All single chars are palindromes.
"aa" is a palindrome.
"aaa" is a palindrome.

Result: [["a","a","a"], ["a","aa"], ["aa","a"], ["aaa"]]
```

4 valid partitions. Every possible way to cut "aaa" into palindromes.

---

## 🔍 Connection to Word Break

| Word Break                        | Palindrome Partitioning           |
| --------------------------------- | --------------------------------- |
| Check if prefix ∈ dictionary      | Check if prefix is palindrome     |
| Return true/false                 | Return ALL valid partitions       |
| DP (memoization/tabulation)       | Backtracking (collect all paths)  |
| `solve(start)` returns bool       | `backtrack(start)` collects results |

Same "front partitioning" structure.
Different validation (dictionary vs palindrome).
Different goal (existence vs enumeration).

---

## 🔍 Optimization -- Precompute Palindromes

Instead of checking `isPalindrome` every time (O(n) per check),
precompute a 2D table:

```cpp
vector<vector<bool>> isPalin(n, vector<bool>(n, false));
for (int len = 1; len <= n; len++) {
    for (int i = 0; i + len - 1 < n; i++) {
        int j = i + len - 1;
        if (s[i] == s[j] && (len <= 2 || isPalin[i+1][j-1])) {
            isPalin[i][j] = true;
        }
    }
}
```

Then replace `isPalindrome(s, start, end)` with `isPalin[start][end]`.
O(1) per check after O(n²) precomputation.

For interview purposes, the simple two-pointer check is usually sufficient.

---

## 🔍 Why Every Single Character Is Always a Palindrome

A single character `"a"` is trivially a palindrome.
So the partition `["a", "a", ..., "a"]` (all single characters)
is ALWAYS valid. Every string has at least one valid partition.

---

### 🧠 Memory of the Mirror Cuts Law

-   **Front partitioning:** at position `start`, try every prefix `s[start..end]`
-   **Only proceed if prefix is a palindrome** (pruning)
-   **Base case:** `start == n` → partition complete, add to result
-   **Palindrome check:** two pointers from both ends, O(n)
-   **Same structure as Word Break** but collect all paths instead of true/false
-   **Optimization:** precompute palindrome table for O(1) checks
-   **Every string has at least one partition** (all single characters)
-   **Time:** O(2ⁿ × n) worst case
-   **Space:** O(n) recursion depth
-   **Edge cases:**
    -   Single character → [["a"]]
    -   Entire string is palindrome → includes the whole string as one partition
    -   All same characters → many partitions (every cut combination works)

Thus is remembered the saga of **Palindrome Partitioning**,
where the Oracle stood at each position in the string
and tried every possible first cut --
only proceeding when the piece was a mirror,
a palindrome reading the same forwards and backwards --
recursing on the remainder,
cutting again and again --
until the entire string was consumed
and every piece was a perfect reflection. 🪞✂️✨
