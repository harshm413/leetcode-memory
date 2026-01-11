## ⚔️🌗 _The Dance of Light and Shadow: The Rearrange Array Elements by Sign Saga_

> \*"In the Realm of Balanced Forces,
> warriors arrived carrying two kinds of energy —
> **positive** and **negative**.
>
> Chaos ruled the formation:
> light clustered with light,
> shadow clung to shadow.
>
> The Oracle was commanded:
>
> **‘Rearrange the warriors
> so Light and Shadow march alternately —
> beginning with Light —
> while preserving the order
> within each faction.’**
>
> No warrior could change allegiance.
> No relative order could be broken.
>
> Only a disciplined weave
> could restore balance."\*

---

This is the saga of **Rearrange Array Elements by Sign**.

You are given an array `nums` such that:

-   The number of **positive** elements equals
    the number of **negative** elements
-   Order **within positives** and **within negatives**
    must be preserved

Your task:

-   Rearrange the array so that:

    ```
    positive, negative, positive, negative, ...
    ```

-   Starting with a **positive** number

---

## 🧠 The Oracle’s Core Insight — Two Rivers Must Interleave

The Oracle observed:

-   Positives and negatives already have an internal order
-   Mixing them directly would destroy that order
-   Instead, **separate first, then weave**

So she chose:

1. Collect positives
2. Collect negatives
3. Interleave them one by one

Balance would be restored without disorder.

---

### 📜 The Scroll of Dual Energies

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚔️ The Oracle’s Separation Ritual

_Divide Light from Shadow_

```cpp
vector<int> rearrangeArray(vector<int>& nums) {
    vector<int> pos, neg;
```

Two paths were prepared —
one for Light, one for Shadow.

---

### 🧭 Walk the Realm and Separate

```cpp
    for (int x : nums) {
        if (x > 0) pos.push_back(x);
        else neg.push_back(x);
    }
```

Each warrior joined their rightful faction
without disturbing internal order.

---

## 🌗 The Oracle’s Weaving Ritual

_Alternate Light and Shadow_

```cpp
    vector<int> result;
    for (int i = 0; i < pos.size(); i++) {
        result.push_back(pos[i]);
        result.push_back(neg[i]);
    }
    return result;
}
```

One by one, Light stepped forward,
followed immediately by Shadow.

The march was perfectly alternating.

---

### 🎺 The Trial of Balance

```cpp
int main() {
    vector<int> nums = {3, 1, -2, -5, 2, -4};
    vector<int> ans = rearrangeArray(nums);

    for (int x : ans) cout << x << " ";
    cout << endl;
    return 0;
}
```

The Oracle restored harmony:

`3 -2 1 -5 2 -4`

Light and Shadow marched together,
balanced and orderly.

---

### 🧠 Memory of the Balanced Weave

-   Separate positives and negatives
-   Preserve internal order
-   Interleave starting with positive
-   Equal counts guaranteed
-   **Time:** O(n)
-   **Space:** O(n)

Thus is remembered the saga of
**Rearrange Array Elements by Sign**,
where the Oracle does not force balance by chaos,
but instead honors each faction’s order —
weaving Light and Shadow together
into a flawless alternating march. 🌗✨
