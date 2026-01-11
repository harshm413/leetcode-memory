## 🔁📜 _The Mirror of Characters: The Reverse String Saga_

> \*"In the Scriptorium of Letters,
> characters lay written in a proud line,
> each knowing its place in the story.
>
> The Oracle was commanded:
>
> **‘Reverse the script.
> Let the last character speak first,
> and the first take the final bow.’**
>
> No new parchment could be used.
> No second scroll could be written.
>
> The transformation had to happen
> **in place**,
> using only the Mirror of Swaps.
>
> Thus began the simplest,
> yet most elegant ritual of reflection."\*

---

This is the saga of **Reverse String**.

You are given a character array `s`.
Your task:

-   Reverse the string **in-place**
-   Use **O(1) extra space**

---

## 🧠 The Oracle’s Core Insight — Reflection from Both Ends

The Oracle knew:

-   The first character must trade places with the last
-   The second with the second-last
-   And so on…

So she placed **two pointers** at opposite ends
and moved them inward,
swapping reflections as they met.

---

### 📜 The Scroll of Mutable Characters

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🔮 The Oracle’s Mirror Ritual

_Swap reflections until the center is reached_

```cpp
void reverseString(vector<char>& s) {
    int left = 0;
    int right = s.size() - 1;
```

Two mirrors were placed:

-   `left` at the beginning
-   `right` at the end

---

### 🔁 Walk Toward the Center

```cpp
    while (left < right) {
        swap(s[left], s[right]);
        left++;
        right--;
    }
}
```

With each swap:

-   The outer characters found their new destiny
-   The mirrors moved closer
-   Until the center stood complete

---

### 🎺 The Trial of the Reflected Script

```cpp
int main() {
    vector<char> s = {'h', 'e', 'l', 'l', 'o'};
    reverseString(s);

    for (char c : s) cout << c;
    cout << endl; // expected: olleh
    return 0;
}
```

The Oracle reflected the script:

`h e l l o → o l l e h`

No extra space.
Pure symmetry.

---

### 🧠 Memory of the Mirror Law

-   Two pointers from both ends
-   Swap characters in-place
-   Move inward until pointers meet
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Reverse String**,
where the Oracle uses nothing but mirrors and balance,
turning the beginning into the end
and the end into the beginning —
proving that sometimes,
the simplest spells
are the most powerful. 🔁✨
