## 🪞📜 _The Trial of Pure Reflection: The Valid Palindrome Saga_

> \*"In the Hall of Letters,
> symbols gathered from many tongues —
> letters and digits stood proud,
> while spaces and punctuation whispered distractions.
>
> The Oracle was commanded:
>
> **‘Judge whether this inscription
> reads the same forward and backward —
> ignoring all noise,
> and seeing only true characters.’**
>
> The challenge was not the mirror,
> but the clutter around it.
>
> Thus the Oracle vowed to walk from both ends,
> filtering truth from noise,
> until reflection proved harmony…
> or exposed a lie."\*

---

This is the saga of **Valid Palindrome**.

You are given a string `s`.
Your task:

-   Consider **only alphanumeric characters**
-   Ignore **case differences**
-   Determine if the string is a **palindrome**

---

## 🧠 The Oracle’s Core Insight — Truth Lies Beneath the Noise

The Oracle realized:

-   Punctuation, spaces, and symbols must be ignored
-   Letters must be compared **case-insensitively**
-   A palindrome mirrors perfectly **after cleansing**

So she used:

-   **Two pointers**
-   One from the left, one from the right
-   Skipping all that is not alphanumeric

---

### 📜 The Scroll of Cleansed Words

```cpp
#include <iostream>
#include <string>
#include <cctype>
using namespace std;
```

---

## ⚔️ The Oracle’s Two-Mirror Ritual

_Cleanse, compare, and converge_

```cpp
bool isPalindrome(string s) {
    int left = 0;
    int right = s.size() - 1;
```

Two mirrors were placed
at opposite ends of the inscription.

---

### 🧹 Skip the Noise

```cpp
    while (left < right) {
        while (left < right && !isalnum(s[left])) left++;
        while (left < right && !isalnum(s[right])) right--;
```

The Oracle ignored:

-   spaces
-   punctuation
-   symbols

Only true characters were allowed to speak.

---

### 🪞 Compare Reflections

```cpp
        if (tolower(s[left]) != tolower(s[right])) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

If reflections mismatched,
the inscription failed the trial.

If all matched,
harmony was confirmed.

---

### 🎺 The Trial of the Sacred Inscription

```cpp
int main() {
    string s = "A man, a plan, a canal: Panama";
    cout << isPalindrome(s) << endl; // expected: 1 (true)
    return 0;
}
```

After cleansing, the inscription became:

```
amanaplanacanalpanama
```

A perfect mirror.

---

### 🧠 Memory of the Reflection Law

-   Ignore non-alphanumeric characters
-   Compare characters case-insensitively
-   Use two pointers from both ends
-   Skip noise, compare truth
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Valid Palindrome**,
where the Oracle looks past noise and distraction,
seeking only the essence of characters —
and by walking toward the center from both ends,
reveals whether the inscription
is a true reflection of itself. 🪞✨
