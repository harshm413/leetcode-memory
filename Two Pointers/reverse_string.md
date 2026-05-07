## 🔄🔤 _The Mirror Swap: The Reverse String Saga_

> \_"The Oracle was given an array of characters.
>
> She was commanded:
>
> **'Reverse the string in-place.
> Do not allocate extra space for another array.
> You must modify the input array with O(1) extra memory.'**
>
> The Oracle placed two pointers:
> one at the beginning, one at the end.
> They walked toward each other, swapping at every step.
> When they met — the string was reversed.
>
> The simplest two-pointer problem.
> The foundation of the entire pattern."\_

---

This is the saga of **Reverse String (LeetCode 344)**.

Given a character array `s`:
-   Reverse it in-place with O(1) extra memory.

```
Input:  s = ['h','e','l','l','o']
Output: ['o','l','l','e','h']

Input:  s = ['H','a','n','n','a','h']
Output: ['h','a','n','n','a','H']
```

---

## 🧠 The Two-Pointer Approach

Two pointers start at opposite ends.
They swap the characters they point to.
Both move inward by one step.
Repeat until they meet or cross.

```
left = 0, right = n-1.
Swap s[left] and s[right].
left++, right--.
Stop when left >= right.
```

Why does this work? After each swap:
-   The outermost unswapped pair gets swapped.
-   The problem shrinks by 2 (one from each end).
-   When left meets right — every pair has been swapped. Done.

> _"Two warriors walk from opposite ends of the hall.
> At each step, they exchange their positions.
> When they meet in the middle — the hall is mirrored."_

---

### 📜 The Scroll of the Mirror Swap

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔄 The Solution

```cpp
void reverseString(vector<char>& s) {
    int left = 0, right = s.size() - 1;
```

Two pointers: `left` at the start, `right` at the end.

---

```cpp
    while (left < right) {
        swap(s[left], s[right]);
        left++;
        right--;
    }
}
```

Swap the pair. Move both inward. Stop when they meet.

`left < right` (not `<=`): when `left == right`, it's the middle element
of an odd-length string. It doesn't need swapping — it stays in place.

---

### 🎺 The Trial

```cpp
int main() {
    vector<char> s1 = {'h','e','l','l','o'};
    reverseString(s1);
    for (char c : s1) cout << c;
    cout << endl; // expected: olleh

    vector<char> s2 = {'H','a','n','n','a','h'};
    reverseString(s2);
    for (char c : s2) cout << c;
    cout << endl; // expected: hannaH

    return 0;
}
```

---

**Trace for s = ['h','e','l','l','o']:**

| Step | left | right | Swap | Array |
|------|------|-------|------|-------|
| 1 | 0 | 4 | h ↔ o | [**o**,e,l,l,**h**] |
| 2 | 1 | 3 | e ↔ l | [o,**l**,l,**e**,h] |
| 3 | 2 | 2 | left==right, stop | [o,l,**l**,e,h] |

**Result: "olleh"** ✓

Middle element 'l' (index 2) stays in place — it's its own mirror.

---

**Trace for s = ['H','a','n','n','a','h']:**

| Step | left | right | Swap | Array |
|------|------|-------|------|-------|
| 1 | 0 | 5 | H ↔ h | [h,a,n,n,a,H] |
| 2 | 1 | 4 | a ↔ a | [h,a,n,n,a,H] (same chars) |
| 3 | 2 | 3 | n ↔ n | [h,a,n,n,a,H] (same chars) |
| 4 | 3 | 2 | left>right, stop | |

**Result: "hannaH"** ✓

Palindromic middle stays the same — swapping identical characters is a no-op.

---

## 🔍 Where Reverse Appears as a Building Block

| Problem | How reverse is used |
|---------|-------------------|
| **Reverse String (this)** | Direct |
| Rotate Array | Three reversals |
| Reverse Words in a String | Reverse all, then reverse each word |
| Valid Palindrome | Compare with reverse (or two pointers) |
| Reverse Linked List | Same concept, different data structure |

Reverse is the most fundamental two-pointer operation.
Master this and the pattern applies everywhere.

---

## 🔍 Recursive Alternative

```cpp
void reverseString(vector<char>& s, int left, int right) {
    if (left >= right) return;
    swap(s[left], s[right]);
    reverseString(s, left + 1, right - 1);
}
```

Same logic, recursive. O(N) stack space — violates the O(1) space requirement.
The iterative version is preferred.

---

## 🔍 Why Not Use `std::reverse`?

```cpp
reverse(s.begin(), s.end());
```

One line. Works. But the problem is testing whether YOU can implement it.
The point is understanding the two-pointer swap pattern.

---

### 🧠 Memory of the Mirror Swap Law

-   **Two pointers:** `left = 0`, `right = n-1`
-   **While `left < right`:** swap, move both inward
-   **Odd length:** middle element stays (left == right, loop exits)
-   **Even length:** all elements get swapped
-   **Time:** O(N). **Space:** O(1).
-   **Foundation** for rotate array, reverse words, and many other problems.

Thus is remembered the saga of **Reverse String**,
where two pointers walked from opposite ends —
swapping at every step, moving inward —
until they met in the middle
and the string was perfectly mirrored. 🔄🔤✨
