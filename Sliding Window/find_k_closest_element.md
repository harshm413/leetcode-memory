## ⚖️📜 _The Council of Nearness: The Find K Closest Elements Saga_

> \*"In the Realm of Ordered Stones,
> numbers lay in perfect ascending calm.
>
> At the center stood a sacred value — **x**.
>
> The Oracle was commanded:
>
> \*\*‘Choose exactly `k` stones
> that stand closest to `x`.
>
> If distances are equal,
> honor the smaller stone.’\*\*
>
> She knew this was not a hunt for chaos,
> but a **measured narrowing of focus**.
>
> Rather than picking stones one by one,
> the Oracle chose to **remove the farthest**
> until only the chosen council remained."\*

---

This is the saga of **Find K Closest Elements**.

You are given:

-   a **sorted** array `arr`
-   integers `k` and `x`

Your task:

-   Return the `k` elements closest to `x`
-   Result must be in **ascending order**
-   Tie-breaker: prefer the **smaller element**

---

## 🧠 The Oracle’s Core Insight — Shrink from the Outside

The Oracle realized:

-   Since the array is sorted,
    the **farthest elements** must lie at the ends
-   A window of size `k` can be formed by
    **removing elements one by one** from either side
-   Compare distances from `x` to decide which end to discard

So she used:

-   Two pointers: `left` and `right`
-   Shrink until window size becomes `k`

---

## ⚔️ The Oracle’s Shrinking Window Ritual

_Discard the farthest, keep the closest_

```cpp
vector<int> findClosestElements(vector<int>& arr, int k, int x) {
    int left = 0;
    int right = arr.size() - 1;
```

The Oracle placed sentinels
at the beginning and end of the realm.

---

### 🔻 Remove Stones Until Only k Remain

```cpp
    while (right - left + 1 > k) {
        if (abs(arr[left] - x) > abs(arr[right] - x)) {
            left++;
        } else {
            right--;
        }
    }
```

At each step:

-   Compare which end is **farther from `x`**
-   Remove that stone from consideration
-   On ties, remove the **larger element** (right side)

---

### 🏁 Return the Chosen Council

```cpp
    return vector<int>(arr.begin() + left, arr.begin() + right + 1);
}
```

What remained was the closest council —
already sorted.

---

### 🎺 The Trial of Nearness

```cpp
int main() {
    vector<int> arr = {1, 2, 3, 4, 5};
    int k = 4;
    int x = 3;

    vector<int> ans = findClosestElements(arr, k, x);
    for (int v : ans) cout << v << " ";
    cout << endl; // expected: 1 2 3 4
    return 0;
}
```

The Oracle chose the four stones
closest to `3`.

---

### 🧠 Memory of the Nearness Law

-   Array is sorted
-   Use two pointers at both ends
-   Shrink window until size = k
-   Remove the farther element
-   Tie → discard the larger value
-   **Time:** O(n − k)
-   **Space:** O(1) (excluding output)

Thus is remembered the saga of
**Find K Closest Elements**,
where the Oracle does not greedily collect stones,
but instead calmly **removes what does not belong**,
until only the closest remain —
a perfectly balanced council
standing nearest to the sacred value. ⚖️✨
