## 🎲👑 _The Whispering Oracle’s Challenge: The Guess Number Higher or Lower Saga_

> \*"In a kingdom of hidden numbers,
> the King chose a secret value between 1 and `n`.
>
> The Oracle was summoned and told:
>
> \*\*‘You may guess the number.
> After each guess,
> you will be told one of three whispers:
>
> • -1 → The number is lower
> • 1 → The number is higher
> • 0 → You have found it’\*\*
>
> The Oracle could not guess blindly.
>
> Instead, she trusted the ancient ritual of halving —
> narrowing the unknown
> until the secret stood alone."\*

---

This is the epic saga of **Guess Number Higher or Lower**.

You are given:

-   an integer `n`
-   a function:

    ```cpp
    int guess(int num);
    ```

    which returns:

    -   `-1` if secret number < num
    -   `1` if secret number > num
    -   `0` if num is correct

Your task:

-   Return the secret number.

---

## 🧠 The Oracle’s Core Insight — Binary Search on the Unknown

Since the number lies between `1` and `n`,
each guess reveals direction.

Thus:

-   Start with full range `[1, n]`
-   Guess the middle
-   Halve the range based on response

---

## ⚔️ The Oracle’s Guessing Ritual

```cpp
int guessNumber(int n) {
    long left = 1;
    long right = n;
```

Two boundaries guard the unknown.

---

### 🌊 Narrow the Range

```cpp
    while (left <= right) {
        long mid = left + (right - left) / 2;
```

The Oracle guesses the middle value.

---

### 🎯 Ask the King

```cpp
        int res = guess(mid);
```

The whisper is received.

---

### 👑 If Correct

```cpp
        if (res == 0) {
            return mid;
        }
```

The secret is revealed.

---

### 🔻 If Secret is Lower

```cpp
        else if (res == -1) {
            right = mid - 1;
        }
```

Eliminate the upper half.

---

### 🔺 If Secret is Higher

```cpp
        else {
            left = mid + 1;
        }
    }
    return -1;
}
```

Eliminate the lower half.

---

## 🧠 Memory of the Whispering Law

-   Apply binary search
-   Use feedback to shrink range
-   Continue until found
-   Avoid overflow with safe mid calculation
-   **Time:** O(log n)
-   **Space:** O(1)

---

Thus is remembered the saga of
**Guess Number Higher or Lower**,
where the Oracle does not guess wildly —
but listens carefully to each whisper,
halving uncertainty again and again,
until the hidden number
stands revealed in certainty. 🎲✨
