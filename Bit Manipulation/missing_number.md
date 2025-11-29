## 🕳️ _The Vanished Spirit: The Missing Number Saga_

> \*"In a kingdom of numbered spirits from 0 to n,
> all stood in a perfect row —
> except one.
>
> A silent absence lingered like a broken note in a song.
> The Oracle was summoned:
> ‘Find the vanished spirit.
> Discover which number has slipped into the void.’
>
> She answered not with searching,
> but with **balance** —
> for every spirit has a place,
> and missing balance reveals the missing one."\*

---

In the Realm of Sequential Spirits,
an array was given containing **all numbers from 0 to n except one**.
The vanished spirit — the missing number —
could be anywhere between 0 and n.

The Oracle knew she could find it swiftly
using the ancient laws of **XOR** or **Arithmetic Balance**.

Thus began the saga of **Missing Number**.

---

### 📜 The Scroll of Sequential Spirits

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

A list of numbers was brought forth,
one number absent, hidden in the void.

---

## ⚡ Method 1 — The Oracle’s XOR Balance

_XOR restores symmetry and reveals the missing spirit_

```cpp
int missingNumber(vector<int>& nums) {
    int xorSum = 0;
    int n = nums.size();
```

She began with a neutral XOR value of zero.

---

```cpp
    for (int i = 0; i <= n; i++) xorSum ^= i;
    for (int x : nums) xorSum ^= x;
```

The Oracle XORed:

1. All numbers from **0 to n**
2. All numbers given in the list

Every number appears **twice** except the missing one.
XOR cancels matching pairs,
leaving only the vanished spirit glowing in the darkness.

---

```cpp
    return xorSum;
}
```

The survivor of XOR was the answer.

---

## ➕ Method 2 — The Arithmetic Balance

_(Optional wisdom of the Oracle)_

`total = n*(n+1)/2`
`missing = total - sum(nums)`

But XOR was more elegant.

---

### 🎺 The Trial of the Vanished Spirit

```cpp
int main() {
    vector<int> nums = {3, 0, 1};
    cout << missingNumber(nums) << endl; // expected: 2
    return 0;
}
```

The spirits present were `[3, 0, 1]`.
From 0 to 3, the balanced world should be `[0, 1, 2, 3]`.
The absent one, left after all XOR symmetries, was **2**.

The Oracle nodded —
the vanished spirit was found.

---

### 🧠 Memory of the Vanishing

-   XOR of full range 0..n and array reveals the missing number.
-   Duplicate XORs cancel each other:
    `x ^ x = 0`, `0 ^ y = y`
-   Alternatively, use arithmetic sum.
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Missing Number**,
where a single vanished spirit hides among many,
but the Oracle restores balance
and reveals the one who slipped into the void. 🕳️✨
