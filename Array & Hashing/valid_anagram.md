## 🔄📜 _The Twin Scrolls: The Valid Anagram Saga_

> \_"In the Land of Scribes,
> two scrolls were discovered --
> their words different,
> their letters scattered in seeming chaos.
>
> Yet whispers spread:
>
> **'Perhaps they are not strangers at all,
> but the same song sung in shuffled order.'**
>
> The Oracle was summoned to judge:
>
> **'Are these two scrolls anagrams --
> do they contain exactly the same letters,
> each appearing exactly the same number of times?'**
>
> The Oracle knew two approaches:
>
> **Approach One -- The Frequency Array:**
> For lowercase English letters only,
> use a fixed array of 26 counters.
> Increment for the first scroll.
> Decrement for the second scroll.
> If all counters return to zero -- anagram confirmed.
> O(N) time, O(1) space.
>
> **Approach Two -- The Hash Map:**
> For any character set (Unicode, mixed case),
> use a hash map to count frequencies.
> Same logic -- increment, decrement, check for zero.
> O(N) time, O(K) space where K = unique characters.
>
> The Oracle chose the frequency array --
> for the problem guaranteed lowercase English letters,
> and 26 counters were all she needed."\_

---

This is the saga of **Valid Anagram**.

You are given two strings `s` and `t`.

Your task:

-   Return `true` if `t` is an anagram of `s`, `false` otherwise.
-   An anagram uses the exact same letters, each the exact same number of times.

```
Input:  s = "anagram", t = "nagaram"
Output: true

Input:  s = "rat", t = "car"
Output: false

Input:  s = "listen", t = "silent"
Output: true
```

---

## 🧠 The Oracle's Core Insight -- Count and Cancel

Two strings are anagrams if and only if
they have the **exact same character frequencies**.

The simplest way to verify this:

> **Use one frequency array.
> Increment for each character in `s`.
> Decrement for each character in `t`.
> If every counter is zero at the end -- they are anagrams.**

If the lengths differ, they cannot be anagrams -- return `false` immediately.

For lowercase English letters, a fixed array of size 26 suffices.
`count[c - 'a']` maps each letter to an index from 0 to 25.

```
Time:  O(N) -- two passes through the strings
Space: O(1) -- fixed array of 26 integers (constant)
```

---

### 📜 The Scroll of Judgment

```cpp
#include <iostream>
#include <string>
using namespace std;
```

---

## ⚖️ The Oracle's Frequency Array Ritual

```cpp
bool isAnagram(string s, string t) {
    if (s.size() != t.size()) return false;
```

The Oracle measured both scrolls.

If their lengths differed -- instant verdict: `false`.
Anagrams must have the same number of characters.

---

### 📝 Build the Frequency Ledger

```cpp
    int count[26] = {0};
```

The Oracle laid out 26 counters --
one for each letter from `a` to `z` --
all initialized to zero.

This was the **Frequency Ledger** --
a compact, constant-space structure
that would track the balance between the two scrolls.

---

### ☀️ Increment for the First Scroll

```cpp
    for (char c : s) {
        count[c - 'a']++;
    }
```

The Oracle walked the first scroll letter by letter.

Each letter `c` incremented its counter.
`c - 'a'` converted the character to an index:
`'a'` became 0, `'b'` became 1, ..., `'z'` became 25.

After this pass, `count[i]` held the frequency
of the `i`th letter in string `s`.

---

### 🌑 Decrement for the Second Scroll

```cpp
    for (char c : t) {
        count[c - 'a']--;
        if (count[c - 'a'] < 0) return false;
    }
```

The Oracle walked the second scroll,
decrementing each letter's counter.

If any counter dropped below zero --
`t` had more of that letter than `s` did.
The scrolls could not be anagrams.
Return `false` immediately.

> _"A counter below zero means the second scroll
> carries a letter the first scroll cannot match.
> The balance is broken. The judgment is swift."_

The early return is an optimization --
we don't need to finish the second pass
if a mismatch is already detected.

---

## 🌅 The Judgment

```cpp
    return true;
}
```

If the second scroll was fully processed
without any counter going negative --
and both scrolls had the same length --
then every counter was exactly zero.

The two scrolls contained the same letters
in the same quantities. They were true anagrams.

---

### 🎺 The Trial of the Twin Scrolls

```cpp
int main() {
    cout << (isAnagram("anagram", "nagaram") ? "true" : "false") << endl;
    // expected: true

    cout << (isAnagram("rat", "car") ? "true" : "false") << endl;
    // expected: false

    cout << (isAnagram("listen", "silent") ? "true" : "false") << endl;
    // expected: true

    cout << (isAnagram("a", "ab") ? "true" : "false") << endl;
    // expected: false

    return 0;
}
```

---

**Full trace for `s = "anagram"`, `t = "nagaram"`:**

**After incrementing for `s = "anagram"`:**

| Letter | a | n | g | r | m |
| ------ | - | - | - | - | - |
| Count  | 3 | 1 | 1 | 1 | 1 |

(All other letters remain 0.)

**Decrementing for `t = "nagaram"`:**

| Step | c | count[c] before | count[c] after | Negative? |
| ---- | - | --------------- | -------------- | --------- |
| 1    | n | 1               | 0              | No        |
| 2    | a | 3               | 2              | No        |
| 3    | g | 1               | 0              | No        |
| 4    | a | 2               | 1              | No        |
| 5    | r | 1               | 0              | No        |
| 6    | a | 1               | 0              | No        |
| 7    | m | 1               | 0              | No        |

All counters returned to zero. No negatives detected.

**Answer: true** ✓

---

**Full trace for `s = "rat"`, `t = "car"`:**

**After incrementing for `s = "rat"`:**

| Letter | r | a | t |
| ------ | - | - | - |
| Count  | 1 | 1 | 1 |

**Decrementing for `t = "car"`:**

| Step | c | count[c] before | count[c] after | Negative? |
| ---- | - | --------------- | -------------- | --------- |
| 1    | c | 0               | -1             | **Yes**   |

`c` was never in `s`. Counter went negative. Return `false` immediately.

**Answer: false** ✓

The Oracle didn't even need to check `a` and `r` --
the first mismatch was enough.

---

## 🔄 Alternative -- Hash Map (For Unicode/Mixed Case)

If the input can contain any characters (not just lowercase English):

```cpp
bool isAnagram(string s, string t) {
    if (s.size() != t.size()) return false;

    unordered_map<char, int> count;
    for (char c : s) count[c]++;
    for (char c : t) {
        count[c]--;
        if (count[c] < 0) return false;
    }
    return true;
}
```

Same logic, but uses a hash map instead of a fixed array.
Works for any character set. O(N) time, O(K) space.

---

### 🧠 Memory of the Twin Scrolls Law

-   **Length check first:** if `s.size() != t.size()` -- return `false` immediately
-   **Frequency array** `count[26]` -- one counter per lowercase letter
-   **Increment** for each character in `s`: `count[c - 'a']++`
-   **Decrement** for each character in `t`: `count[c - 'a']--`
-   **Early exit:** if any counter goes negative -- `t` has a letter `s` doesn't -- return `false`
-   If all counters remain non-negative after both passes -- return `true`
-   **Hash map alternative** for Unicode/mixed case -- same logic, flexible character set
-   **Time:** O(N) -- two linear passes
-   **Space:** O(1) for frequency array (26 is constant), O(K) for hash map

Thus is remembered the saga of **Valid Anagram**,
where the Oracle laid out 26 counters
and walked two scrolls in sequence --
incrementing for the first,
decrementing for the second --
and when every counter returned to zero,
the twin scrolls were revealed as true anagrams,
the same song sung in shuffled order. 🔄📜✨
