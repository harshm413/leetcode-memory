## 🎭👑 _The Silent One in the Court: The Celebrity Problem Saga_

> \*"In the Grand Royal Court stood `n` nobles.
> Some knew many.
> Some were known by none.
>
> But rumor spoke of one special figure —
> a **Celebrity**.
>
> The Oracle was commanded:
>
> **‘Find the Celebrity, if one exists.’**
>
> The law of the court was strict:
>
> • The Celebrity knows **no one**.
> • Everyone else knows **the Celebrity**.
>
> The Oracle could only ask one question:
>
> `Does A know B?`
>
> And she must ask as few questions as possible."\*

---

This is the epic saga of the **Celebrity Problem**.

You are given:

-   `n` people labeled `0` to `n-1`
-   A helper function:

    ```
    bool knows(a, b);
    ```

Your task:

-   Return the index of the Celebrity
-   Or `-1` if none exists

---

## 🧠 The Oracle’s Core Insight — Eliminate in Pairs

If:

-   `A` knows `B` → `A` cannot be celebrity
-   If `A` does NOT know `B` → `B` cannot be celebrity

Thus, in one comparison:

-   One candidate is eliminated.

Repeat this elimination
until only **one candidate remains**.

Then verify that candidate.

---

## ⚔️ The Oracle’s Elimination Ritual

### Step 1: Find Potential Candidate

```cpp
int findCelebrity(int n) {
    int candidate = 0;

    for (int i = 1; i < n; i++) {
        if (knows(candidate, i)) {
            candidate = i;
        }
    }
```

By the end:

-   Only one possible celebrity remains.

---

## 🧪 Step 2: Verify the Candidate

Now ensure:

-   Candidate knows no one
-   Everyone knows candidate

```cpp
    for (int i = 0; i < n; i++) {
        if (i != candidate) {
            if (knows(candidate, i) || !knows(i, candidate)) {
                return -1;
            }
        }
    }

    return candidate;
}
```

If verification fails → no celebrity.

---

## 🎺 The Logic in Action

Imagine:

-   Person 0 knows 1 → eliminate 0
-   Person 1 knows 2 → eliminate 1
-   Person 2 survives all

Now verify:

-   2 knows no one
-   Everyone knows 2

If true → 2 is Celebrity.

---

## 🧠 Memory of the Court Law

-   Use elimination to find candidate
-   One pass to reduce to single person
-   Second pass to verify
-   Total queries: O(n)
-   **Time:** O(n)
-   **Space:** O(1)

---

Thus is remembered the saga of
**The Celebrity Problem**,
where the Oracle does not test every pair blindly —
but eliminates pretenders one by one,
until only one silent figure remains —
a person known by all,
yet knowing none. 🎭✨
