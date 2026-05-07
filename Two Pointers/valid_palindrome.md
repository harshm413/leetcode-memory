## 🪞✅ _The Filtered Mirror: The Valid Palindrome Saga_

> \_"The Oracle was given a string with letters, digits, spaces, and punctuation.
>
> She was commanded:
>
> **'Determine if it is a palindrome,
> considering only alphanumeric characters
> and ignoring cases.'**
>
> `"A man, a plan, a canal: Panama"` → after filtering: `"amanaplanacanalpanama"` → palindrome ✓.
>
> The Oracle used two pointers from opposite ends.
> But before comparing, she SKIPPED non-alphanumeric characters
> and converted to lowercase.
>
> Skip. Compare. Skip. Compare.
> If all comparisons match — palindrome."\_

---

This is the saga of **Valid Palindrome (LeetCode 125)**.

Given a string `s`:
-   Return `true` if it's a palindrome after removing non-alphanumeric characters
    and converting to lowercase.

```
Input:  s = "A man, a plan, a canal: Panama"
Output: true

Input:  s = "race a car"
Output: false   ('r' vs 'c' after filtering)

Input:  s = " "
Output: true   (empty after filtering = palindrome)
```

---

## 🧠 The Two-Pointer + Skip Approach

Two pointers: `left` at start, `right` at end.

At each step:
1. **Skip** non-alphanumeric characters from the left.
2. **Skip** non-alphanumeric characters from the right.
3. **Compare** the two characters (case-insensitive).
4. If they don't match → not a palindrome.
5. If they match → move both inward. Continue.

If all comparisons pass → palindrome.

---

### 📜 The Scroll of the Filtered Mirror

```cpp
#include <iostream>
#include <string>
using namespace std;
```

---

## 🪞 The Solution

### Initialize two pointers

```cpp
bool isPalindrome(string s) {
    int left = 0, right = s.size() - 1;
```

---

### Walk inward, skipping and comparing

```cpp
    while (left < right) {
```

---

### Skip non-alphanumeric from left

```cpp
        while (left < right && !isalnum(s[left])) {
            left++;
        }
```

`isalnum()` returns true for letters and digits.
Skip spaces, punctuation, symbols — anything that's not alphanumeric.

The `left < right` guard prevents going past the other pointer.

---

### Skip non-alphanumeric from right

```cpp
        while (left < right && !isalnum(s[right])) {
            right--;
        }
```

Same skip from the right side.

---

### Compare (case-insensitive)

```cpp
        if (tolower(s[left]) != tolower(s[right])) {
            return false;
        }
```

Convert both to lowercase before comparing.
'A' and 'a' should be treated as equal.

If they don't match — not a palindrome. Return immediately.

---

### Move inward

```cpp
        left++;
        right--;
    }
    return true;
}
```

Characters matched. Move both pointers inward. Continue.
If the loop completes without returning false — it's a palindrome.

---

### 🎺 The Trial of the Filtered Mirror

```cpp
int main() {
    cout << boolalpha;
    cout << isPalindrome("A man, a plan, a canal: Panama") << endl; // true
    cout << isPalindrome("race a car") << endl;                      // false
    cout << isPalindrome(" ") << endl;                               // true
    cout << isPalindrome("0P") << endl;                              // false
    return 0;
}
```

---

**Trace for s = "A man, a plan, a canal: Panama":**

```
left=0('A'), right=29('a'): both alnum. tolower: 'a'=='a' ✓. Move.
left=1(' '): skip. left=2('m').
right=28('m'): alnum. 'm'=='m' ✓. Move.
left=3('a'), right=27('a'): 'a'=='a' ✓. Move.
left=4('n'), right=26('n'): 'n'=='n' ✓. Move.
... (all match symmetrically) ...
```

**Answer: true** ✓

---

**Trace for s = "race a car":**

```
left=0('r'), right=9('r'): 'r'=='r' ✓.
left=1('a'), right=8('a'): 'a'=='a' ✓.
left=2('c'), right=7('c'): 'c'=='c' ✓.
left=3('e'), right=6(' '): skip right. right=5('a').
left=3('e'), right=5('a'): 'e' != 'a' ✗. Return false.
```

**Answer: false** ✓

---

**Trace for s = " ":**

```
left=0(' '): skip. left=1. left > right (right=0). Loop doesn't execute.
Return true.
```

**Answer: true** ✓ (empty after filtering = trivially a palindrome)

---

## 🔍 Why `isalnum` and `tolower`?

**`isalnum(c)`:** returns true if `c` is a letter (a-z, A-Z) or digit (0-9).
Returns false for spaces, punctuation, symbols.

**`tolower(c)`:** converts uppercase to lowercase. Leaves lowercase and digits unchanged.

These are from `<cctype>`. They handle the "ignore non-alphanumeric and case" requirement.

---

## 🔍 Edge Cases

**All non-alphanumeric:** `",.!@#"` → empty after filtering → true (empty palindrome).
**Single character:** `"a"` → true.
**Digits:** `"12321"` → true (digits are alphanumeric).
**Mixed:** `"0P"` → '0' vs 'p' → false.

---

## 🔍 Alternative — Filter First, Then Check

```cpp
bool isPalindrome(string s) {
    string filtered = "";
    for (char c : s) {
        if (isalnum(c)) filtered += tolower(c);
    }
    int left = 0, right = filtered.size() - 1;
    while (left < right) {
        if (filtered[left] != filtered[right]) return false;
        left++; right--;
    }
    return true;
}
```

Simpler logic but O(N) extra space. The in-place skip approach is O(1) space.

---

### 🧠 Memory of the Filtered Mirror Law

-   **Two pointers** from opposite ends
-   **Skip** non-alphanumeric with `isalnum()`
-   **Compare** case-insensitively with `tolower()`
-   **Mismatch** → return false immediately
-   **All match** → return true
-   **Empty after filtering** → true (trivial palindrome)
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Valid Palindrome**,
where the Oracle walked from both ends of the string —
skipping the noise of spaces and punctuation,
comparing only the letters and digits that mattered,
ignoring the difference between upper and lower —
until the filtered mirror was confirmed or denied. 🪞✅✨
