## 🍌⚔️ _The Pace of the Hungry Titan: The Koko Eating Bananas Saga_

> \*"In the Forest of Piles,
> bananas were stacked in towering heaps.
>
> The mighty Koko stood ready to feast —
> but she had only **H hours** before the guards returned.
>
> The Oracle was commanded:
>
> **‘Find the minimum eating speed `k`
> such that Koko can finish all bananas
> within H hours.’**
>
> Too slow — and time would betray her.
> Too fast — and the speed would be wasteful.
>
> The Oracle knew this was not about counting bananas —
> but about searching the correct pace."\*

---

This is the epic saga of **Koko Eating Bananas**.

You are given:

-   `piles[]` → bananas in each pile
-   `h` → number of hours available

Rules:

-   Koko chooses a speed `k` bananas per hour.
-   In one hour, she eats up to `k` bananas from a single pile.
-   If a pile has fewer than `k`, she finishes it in that hour.
-   She cannot split an hour across piles.

Your task:

-   Find the **minimum integer k**
    such that all bananas are eaten within `h` hours.

---

## 🧠 The Oracle’s Core Insight — Binary Search on Speed

The possible speed lies between:

```
1  → slowest
max(piles) → fastest needed
```

For any chosen speed `k`:

Compute total hours required:

```
hours += ceil(pile / k)
```

If:

-   `hours <= h` → speed is valid
-   Otherwise → speed is too slow

Thus we binary search on `k`.

---

## ⚔️ The Oracle’s Pace-Finding Ritual

```cpp
int minEatingSpeed(vector<int>& piles, int h) {
    int left = 1;
    int right = *max_element(piles.begin(), piles.end());
```

Search between minimum and maximum possible speeds.

---

### 🌊 Narrow the Speed Range

```cpp
    while (left < right) {
        int mid = left + (right - left) / 2;
```

Mid represents a candidate speed.

---

### 🧮 Calculate Required Hours

```cpp
        long long hours = 0;
        for (int pile : piles) {
            hours += (pile + mid - 1) / mid; // ceiling division
        }
```

Each pile contributes
the time needed at speed `mid`.

---

### 🔻 If Speed Is Sufficient

```cpp
        if (hours <= h) {
            right = mid;
        }
```

Try a smaller speed.

---

### 🔺 If Speed Is Too Slow

```cpp
        else {
            left = mid + 1;
        }
    }
    return left;
}
```

Increase the speed.

---

### 🎺 The Trial of the Bananas

```cpp
int main() {
    vector<int> piles = {3,6,7,11};
    int h = 8;

    cout << minEatingSpeed(piles, h) << endl;
    // expected: 4
    return 0;
}
```

The Oracle determined:

At speed `4`,
Koko finishes in exactly `8` hours.

No slower speed would suffice.

---

## 🧠 Memory of the Titan’s Pace Law

-   Binary search on answer (speed)
-   Range = [1, max(piles)]
-   Use ceiling division for hours
-   If hours ≤ h → try smaller speed
-   **Time:** O(n log maxPile)
-   **Space:** O(1)

---

Thus is remembered the saga of
**Koko Eating Bananas**,
where the Oracle does not test every speed blindly —
but narrows the pace with disciplined halving,
until the minimum speed is found —
just enough for the hungry titan
to finish her feast before time runs out. 🍌✨
