## 🌋🔥 _The Twin Serpents of Power: The Maximum Product Subarray Saga_

> \*"In the Valley of Numbers,
> the Oracle encountered a shifting trail of integers —
> some blessed, some cursed,
> some bright with positivity,
> and others shadowed by negativity.
>
> Legends warned that **product**, unlike sum,
> is a wild and volatile beast:
>
> a single negative may invert destiny,
> while two negatives may conjure greatness.
>
> The Oracle was tasked:
> **find the contiguous subarray whose product is the greatest
> in the entire valley.**
>
> To achieve this, she summoned two serpents —
> the Serpent of Maximum Power
> and the Serpent of Minimum Power —
> for only together could they capture
> the shifting fate of multiplication."\*

---

The product of a subarray is unpredictable:

-   Multiply by a **positive** → keeps direction
-   Multiply by a **zero** → resets all destiny
-   Multiply by a **negative** → flips max ↔ min

Thus the Oracle kept track of:

-   `curMax` → the greatest product _ending at this position_
-   `curMin` → the smallest product _ending at this position_
-   `ans` → the greatest product seen overall

Every step, the serpents twisted around each other,
for a negative number could make the smallest product
suddenly become the largest.

Thus began the saga of **Maximum Product Subarray**.

---

### 📜 The Scroll of Shifting Numbers

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The valley floor of numbers awaited revelation.

---

## 🔥 The Oracle’s Dance with the Twin Serpents

```cpp
int maxProduct(vector<int>& nums) {
    int curMax = nums[0];
    int curMin = nums[0];
    int ans = nums[0];
```

At the first number,
both serpents coiled around the same value.
The answer began here too.

---

### 🐍 Step Through the Valley

```cpp
    for (int i = 1; i < nums.size(); i++) {
        int x = nums[i];
```

Each new number `x`
carried potential fortune or disaster.

---

### 🔄 If the Number Is Negative, Swap the Serpents

```cpp
        if (x < 0) {
            swap(curMax, curMin);
        }
```

A negative number inverts destinies.
The serpent of power becomes weak,
and the weak becomes mighty.
This swap is essential.

---

### 🐉 Update the Serpent of Maximum Power

### 🐍 Update the Serpent of Minimum Power

```cpp
        curMax = max(x, curMax * x);
        curMin = min(x, curMin * x);
```

From the new number `x`,
each serpent chooses:

-   either to **start anew**
-   or **extend its current product**

The stronger and weaker fates adjust accordingly.

---

### 🔥 Record the Mightiest Product So Far

```cpp
        ans = max(ans, curMax);
    }
    return ans;
}
```

The Oracle marked the highest power achieved
across the entire journey.

---

### 🎺 The Trial of the Volcanic Valley

```cpp
int main() {
    vector<int> nums = {2, 3, -2, 4};
    cout << maxProduct(nums) << endl; // expected: 6 (2 * 3)

    vector<int> nums2 = {-2, 0, -1};
    cout << maxProduct(nums2) << endl; // expected: 0

    return 0;
}
```

The Oracle found:

-   For `{2,3,-2,4}` → the greatest product is **6**
-   For `{-2,0,-1}` → the zero resets destiny → **0**

The serpents never lied.

---

### 🧠 Memory of the Twin-Serpent Law

-   Track `curMax` and `curMin` at every step
-   Swap them when encountering a negative
-   Update via:

    -   `curMax = max(x, curMax * x)`
    -   `curMin = min(x, curMin * x)`

-   The final answer is the greatest `curMax` ever seen
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of
**Maximum Product Subarray**,
where the Oracle walks a volcanic valley,
guided by twin serpents
that twist the fate of multiplication
with every step she takes. 🌋🐍✨
