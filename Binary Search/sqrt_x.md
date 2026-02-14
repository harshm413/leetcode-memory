## 🏛️📐 _The Root Beneath the Stone: The Sqrt(x) Saga_

> \*"In the Realm of Perfect Squares,
> some numbers formed flawless stones —
> 1, 4, 9, 16…
>
> Yet many stood imperfect,
> their true roots hidden beneath the surface.
>
> The Oracle was commanded:
>
> \*\*‘Given a number `x`,
> return its square root —
> but only the integer part.
>
> No decimals.
> No rounding upward.’\*\*
>
> The Oracle knew brute force would be slow.
>
> Instead, she turned to the ancient art of halving —
> narrowing the range
> until the greatest integer
> whose square does not exceed `x`
> stood revealed."\*

---

This is the epic saga of **Sqrt(x)**.

You are given:

-   a non-negative integer `x`

Your task:

-   Compute and return:

    ```
    floor( sqrt(x) )
    ```

-   Do NOT use built-in sqrt.

---

## 🧠 The Oracle’s Core Insight — Binary Search on the Answer

The answer lies between:

```
0 and x
```

At each step:

-   Let `mid`
-   If `mid * mid <= x` → possible answer
-   Otherwise → too large

Keep track of the best valid candidate.

---

## ⚔️ The Oracle’s Root-Finding Ritual

```cpp
int mySqrt(int x) {
    if (x < 2) return x;

    long left = 1;
    long right = x / 2;
    int answer = 0;
```

We use `long`
to avoid overflow when squaring.

---

### 🌊 Narrow the Search

```cpp
    while (left <= right) {
        long mid = left + (right - left) / 2;
        long square = mid * mid;
```

The Oracle tests the middle stone.

---

### 🎯 If Perfect Match

```cpp
        if (square == x) {
            return mid;
        }
```

The exact root is found.

---

### 🔻 If Too Small

```cpp
        else if (square < x) {
            answer = mid;
            left = mid + 1;
        }
```

Mid is valid,
but maybe a larger one exists.

---

### 🔺 If Too Large

```cpp
        else {
            right = mid - 1;
        }
    }
    return answer;
}
```

Mid was too great — shrink the range.

---

### 🎺 The Trial of the Hidden Root

```cpp
int main() {
    cout << mySqrt(8) << endl;  // expected: 2
    cout << mySqrt(16) << endl; // expected: 4
    return 0;
}
```

The Oracle revealed:

-   √8 → 2
-   √16 → 4

The greatest integer whose square
does not exceed `x`.

---

## 🧠 Memory of the Root Law

-   Use binary search on range
-   Compare `mid * mid` with `x`
-   Track last valid answer
-   Use `long` to avoid overflow
-   **Time:** O(log x)
-   **Space:** O(1)

---

Thus is remembered the saga of
**Sqrt(x)**,
where the Oracle does not test every stone,
but halves the field again and again —
until she uncovers the deepest root
hidden beneath the surface of the number. 🏛️✨
