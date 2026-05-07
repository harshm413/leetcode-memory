## 🪞✂️ _The One Forgiveness: The Valid Palindrome II Saga_

> \_"The Oracle was given a string.
>
> She was commanded:
>
> **'Can this string become a palindrome
> by deleting AT MOST one character?'**
>
> If it's already a palindrome — yes (delete zero characters).
> If removing one character makes it a palindrome — yes.
> Otherwise — no.
>
> The Oracle used two pointers from opposite ends.
> When she found a MISMATCH, she had one chance:
>
> **Try skipping the left character. Check if the rest is a palindrome.**
> **Try skipping the right character. Check if the rest is a palindrome.**
>
> If EITHER works — the answer is true.
> If NEITHER works — the answer is false.
>
> One forgiveness. One chance to skip. That's all."\_

---

This is the saga of **Valid Palindrome II (LeetCode 680)**.

Given a string `s`:
-   Return `true` if it can become a palindrome by deleting at most one character.

```
Input:  s = "aba"
Output: true   (already a palindrome)

Input:  s = "abca"
Output: true   (delete 'b' → "aca" or delete 'c' → "aba")

Input:  s = "abc"
Output: false  (no single deletion makes it a palindrome)
```

---

## 🧠 The Approach — Two Pointers + One Forgiveness

Start with two pointers at opposite ends. Compare characters.

**If they match:** move both inward. Continue.
**If they MISMATCH:** this is the moment of forgiveness.
-   Try 1: skip `left` (check if `s[left+1..right]` is a palindrome).
-   Try 2: skip `right` (check if `s[left..right-1]` is a palindrome).
-   If either is a palindrome → return true.
-   If neither → return false.

We only get ONE mismatch. The first mismatch uses our one deletion.
If the remaining substring (after skipping) has another mismatch — it's over.

---

### 📜 The Scroll of the One Forgiveness

```cpp
#include <iostream>
#include <string>
using namespace std;
```

---

## ✂️ Helper — Check if a Substring is a Palindrome

```cpp
bool isPalin(string& s, int left, int right) {
    while (left < right) {
        if (s[left] != s[right]) return false;
        left++;
        right--;
    }
    return true;
}
```

Standard two-pointer palindrome check on a range `[left, right]`.
No skipping allowed here — this is the strict check after using our one deletion.

---

## ✂️ The Main Function

```cpp
bool validPalindrome(string s) {
    int left = 0, right = s.size() - 1;
```

---

### Walk inward, looking for the first mismatch

```cpp
    while (left < right) {
        if (s[left] != s[right]) {
```

Found a mismatch. This is our ONE chance to delete.

---

### Try both options — skip left or skip right

```cpp
            return isPalin(s, left + 1, right) || isPalin(s, left, right - 1);
        }
```

**`isPalin(s, left+1, right)`:** delete `s[left]`. Is the rest a palindrome?
**`isPalin(s, left, right-1)`:** delete `s[right]`. Is the rest a palindrome?

If EITHER returns true — we can make a palindrome with one deletion.
If BOTH return false — no single deletion saves it.

> _"The Oracle faces the first disagreement.
> She tries two futures: skip the left offender, or skip the right.
> If either future is a palindrome — forgiveness granted.
> If neither — the string is beyond saving."_

---

### No mismatch found — move inward

```cpp
        left++;
        right--;
    }
    return true;
}
```

If the loop completes without a mismatch — it's already a palindrome.
No deletion needed. Return true.

---

### 🎺 The Trial of the One Forgiveness

```cpp
int main() {
    cout << boolalpha;
    cout << validPalindrome("aba") << endl;    // true (already palindrome)
    cout << validPalindrome("abca") << endl;   // true (delete 'b' or 'c')
    cout << validPalindrome("abc") << endl;    // false
    cout << validPalindrome("deeee") << endl;  // true (delete 'd')
    cout << validPalindrome("aguokepatgbnvfqmgmlcupuufxoohdfpgjdmysgvhmvffcnqxjjxqncffvmhvgsymd") << endl;
    return 0;
}
```

---

**Trace for s = "abca":**

```
left=0('a'), right=3('a'): match. Move. left=1, right=2.
left=1('b'), right=2('c'): MISMATCH!

Try 1: isPalin("abca", 2, 2) → s[2..2] = "c". Single char → true.
Try 2: isPalin("abca", 1, 1) → s[1..1] = "b". Single char → true.

Either works → return true.
```

**Answer: true** ✓ (delete 'b' → "aca", or delete 'c' → "aba")

---

**Trace for s = "abc":**

```
left=0('a'), right=2('c'): MISMATCH!

Try 1: isPalin("abc", 1, 2) → s[1..2] = "bc". 'b'≠'c' → false.
Try 2: isPalin("abc", 0, 1) → s[0..1] = "ab". 'a'≠'b' → false.

Neither works → return false.
```

**Answer: false** ✓

---

**Trace for s = "deeee":**

```
left=0('d'), right=4('e'): MISMATCH!

Try 1: isPalin("deeee", 1, 4) → "eeee". All match → true.

Return true (don't even need to check try 2).
```

**Answer: true** ✓ (delete 'd' → "eeee")

---

**Trace for s = "aba":**

```
left=0('a'), right=2('a'): match. left=1, right=1.
left >= right. Loop ends. Return true.
```

**Answer: true** ✓ (already a palindrome, no deletion needed)

---

## 🔍 Why Only Check at the FIRST Mismatch?

If the outer characters match, they're fine — no deletion needed there.
The first mismatch is the ONLY place where a deletion could help.

After using our one deletion (skipping left or right),
the remaining substring must be a PERFECT palindrome.
No more chances. That's why `isPalin` is a strict check.

---

## 🔍 Time Complexity — Why It's O(N)

The outer loop runs at most O(N/2) steps before finding a mismatch.
The `isPalin` helper runs at most O(N/2) steps.
Total: O(N). Not O(N²).

We only call `isPalin` ONCE (at the first mismatch). Not at every position.

---

## 🔍 Valid Palindrome I vs II

| Problem | Allowed deletions | Approach |
|---------|-------------------|----------|
| Valid Palindrome I | 0 (just check) | Two pointers, skip non-alnum |
| **Valid Palindrome II** | **At most 1** | **Two pointers + one fork** |
| Valid Palindrome III (LC 1216) | At most K | DP (longest palindromic subsequence) |

Each level adds more deletions. I = direct check. II = one fork. III = full DP.

---

### 🧠 Memory of the One Forgiveness Law

-   **Two pointers** from opposite ends
-   **First mismatch:** try `isPalin(left+1, right)` OR `isPalin(left, right-1)`
-   **Either works** → true. **Neither** → false.
-   **No mismatch** → already a palindrome → true
-   **Only ONE fork** — at the first mismatch. Not recursive.
-   **Time:** O(N). **Space:** O(1).

Thus is remembered the saga of **Valid Palindrome II**,
where the Oracle walked from both ends —
and at the first disagreement, she was granted one forgiveness:
skip the left or skip the right —
if either path led to a perfect palindrome,
the string was saved —
one deletion, one chance, one answer. 🪞✂️✨
