## 👑🗳️ _The Voice of the Many: The Majority Element Saga_

> \*"In the Assembly of Numbers,
> every value raised its voice.
>
> Some spoke once,
> some twice,
> but one voice thundered louder than all others combined.
>
> The Oracle was summoned with a decree:
>
> **‘Find the element that appears more than half the time.’**
>
> No counting scrolls were allowed,
> no ledgers of frequency,
> only a single pass through the assembly.
>
> Thus the Oracle invoked
> the ancient Rite of Balance —
> where opposing voices cancel each other
> until only the true ruler remains."\*

---

The Oracle was given an array `nums`,
and it was guaranteed that a **majority element** existed —
an element that appeared **more than ⌊n / 2⌋ times**.

She chose the legendary spell known as
**Boyer–Moore Voting Algorithm**.

Its wisdom was simple:

-   Treat each different element as an opposing voice
-   Let them cancel each other out
-   The true majority can never be fully canceled

Thus began the saga of **Majority Element**.

---

### 📜 The Scroll of the Great Assembly

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The voices gathered.

---

## ⚖️ The Oracle’s Rite of Balance

_Cancel the weak, preserve the ruler_

```cpp
int majorityElement(vector<int>& nums) {
    int candidate = 0;
    int count = 0;
```

At first, there was no ruler —
no voice strong enough to lead.

---

### 🗣️ Walk Through the Assembly

```cpp
    for (int x : nums) {
```

Each number stepped forward to speak.

---

### 👑 If No Leader Stands, Crown One

```cpp
        if (count == 0) {
            candidate = x;
            count = 1;
        }
```

When balance was lost,
the current speaker was crowned **candidate**.

---

### ⚔️ Supporters vs Opponents

```cpp
        else if (x == candidate) {
            count++;
        } else {
            count--;
        }
    }
```

-   If the voice matched the ruler → support grew
-   If it differed → opposition canceled one supporter

Voices clashed and neutralized each other.

---

### 🏆 The Survivor Is the Ruler

```cpp
    return candidate;
}
```

When the dust settled,
only the true majority could remain standing.

---

### 🎺 The Trial of the Loudest Voice

```cpp
int main() {
    vector<int> nums = {2, 2, 1, 1, 1, 2, 2};
    cout << majorityElement(nums) << endl; // expected: 2
    return 0;
}
```

The voices clashed fiercely,
but **2** outshouted all others combined.

The Oracle declared the winner.

---

### 🧠 Memory of the Voting Rite

-   Majority element appears **more than n/2 times**
-   Pairwise cancellation removes non-majority voices
-   Final candidate is guaranteed to be the majority
-   **Time:** O(n) — single pass
-   **Space:** O(1)

Thus is remembered the saga of **Majority Element**,
where the Oracle listens not to every voice equally,
but allows balance and cancellation
to reveal the one ruler
whose presence can never be silenced. 👑✨
