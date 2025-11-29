## 🔢 _The Rising Flames: The Counting Bits Saga_

> \*"The Realm of Numbers stretched far and wide,
> from 0 up to the mighty n.
> Each number carried a torch of binary fire —
> some torches dim, some blazing bright.
> The Oracle was asked not for one flame,
> but for **all flames** from 0 to n:
>
> ‘Tell us how many sparks burn in each torch.’
>
> To answer, she uncovered a beautiful truth —
> flames rise from ancestors.
> Every number inherits its fire
> from a smaller number with one bit stripped away."\*

---

In the Kingdom of Binary,
the Oracle faced a parade of numbers from **0 to n**.
She needed to compute an array where:

`ans[i] = number of 1 bits in i`

But she refused to recompute each number from scratch.
Instead, she discovered patterns in the river of binary flames:

-   When a number drops its lowest set bit (`i & (i - 1)`),
    it becomes a smaller number whose flames were already known.
-   Therefore:

> `countBits[i] = countBits[i & (i - 1)] + 1`

This revelation turned an impossible task
into a linear-time prophecy.

Thus began the saga of **Counting Bits**.

---

### 📜 The Scroll of Flames

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The Oracle prepared a vessel (a vector)
to store the flame-count of every number.

---

## 🔥 The Oracle’s Prophetic Formula

```cpp
vector<int> countBits(int n) {
    vector<int> res(n + 1, 0);
```

She initialized an array of size `n+1`,
starting with all zero flames.

---

### 🌟 The Lineage of Flames

_Each number inherits from a smaller one_

```cpp
    for (int i = 1; i <= n; i++) {
        res[i] = res[i & (i - 1)] + 1;
    }
    return res;
}
```

For every number `i`:

-   `i & (i - 1)` removes the **lowest burning bit**.
-   The flame-count of this ancestor is already known.
-   Add 1 for the bit that was removed —
    and the new number’s flame-count is revealed.

Elegant. Powerful.
A perfect spell of dynamic inheritance.

---

### 🎺 The Trial of the Binary Parade

```cpp
int main() {
    int n = 5;
    vector<int> ans = countBits(n);

    for (int x : ans) cout << x << " ";
    cout << endl; // expected: 0 1 1 2 1 2
    return 0;
}
```

From 0 to 5,
the flames rose as follows:

-   0 → 0 flames
-   1 → 1 flame
-   2 → 1 flame
-   3 → 2 flames
-   4 → 1 flame
-   5 → 2 flames

The Oracle nodded —
the lineage of flames held true.

---

### 🧠 Memory of the Flame Lineage

-   For every number:

    -   `i & (i - 1)` removes the lowest set bit
    -   So `countBits[i] = countBits[i & (i - 1)] + 1`

-   A dynamic programming prophecy that runs in:

    -   **Time:** O(n)
    -   **Space:** O(n)

-   No bit-by-bit scanning needed.

Thus is remembered the saga of **Counting Bits**,
where every torch inherits its fire from an ancestor,
and the Oracle reveals the rising flames of numbers
from 0 to n in perfect order. 🔥✨
