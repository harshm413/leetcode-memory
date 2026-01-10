## 👑👑 _The Twin Thrones of the Assembly: The Majority Element II Saga_

> \*"In the Great Assembly of Numbers,
> voices rose once more —
> but this time, no single ruler could dominate alone.
>
> The ancient law had changed:
>
> **‘Only those who speak more than one-third of the time
> may claim a throne.’**
>
> At most, two such rulers could exist —
> for three voices of equal power
> would cancel each other into silence.
>
> The Oracle was summoned again,
> armed with a refined Rite of Balance,
> capable of revealing **up to two true leaders**
> among the chaos."\*

---

This is the saga of **Majority Element II**.

You are given an array `nums`.
Your task is to find **all elements** that appear **more than ⌊n / 3⌋ times**.

Key truths the Oracle knew:

-   There can be **at most two** such elements
-   Pairwise cancellation still works,
    but now with **two candidates instead of one**
-   A second pass is required to **verify** the rulers

Thus began the saga of the **Two-Throne Boyer–Moore Rite**.

---

### 📜 The Scroll of the Divided Assembly

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚖️ The Oracle’s Two-Throne Voting Ritual

```cpp
vector<int> majorityElement(vector<int>& nums) {
    int cand1 = 0, cand2 = 0;
    int cnt1 = 0, cnt2 = 0;
```

At first, both thrones were empty.
No ruler yet claimed authority.

---

### 🗣️ First Pass — The Rite of Balance

```cpp
    for (int x : nums) {
        if (x == cand1) {
            cnt1++;
        } else if (x == cand2) {
            cnt2++;
        }
```

If a voice matched an existing ruler,
its support grew stronger.

---

### 👑 Crown New Rulers if a Throne Is Empty

```cpp
        else if (cnt1 == 0) {
            cand1 = x;
            cnt1 = 1;
        } else if (cnt2 == 0) {
            cand2 = x;
            cnt2 = 1;
        }
```

If a throne stood empty,
the current voice was crowned ruler.

---

### ⚔️ Cancel Opposing Voices

```cpp
        else {
            cnt1--;
            cnt2--;
        }
    }
```

When a voice matched neither ruler,
it opposed both —
and balance was restored through cancellation.

---

## 🔍 Second Pass — The Trial of Verification

The Oracle knew better than to trust appearances alone.
She reset the counts.

```cpp
    cnt1 = 0;
    cnt2 = 0;
```

---

### 📊 Count the True Strength of Each Candidate

```cpp
    for (int x : nums) {
        if (x == cand1) cnt1++;
        else if (x == cand2) cnt2++;
    }
```

Now the true voices were counted honestly.

---

### 🏆 Declare the True Rulers

```cpp
    vector<int> res;
    int n = nums.size();
    if (cnt1 > n / 3) res.push_back(cand1);
    if (cnt2 > n / 3) res.push_back(cand2);
    return res;
}
```

Only those who spoke more than ⌊n / 3⌋ times
were allowed to keep their thrones.

---

### 🎺 The Trial of the Dual Rulers

```cpp
int main() {
    vector<int> nums = {1, 1, 1, 3, 3, 2, 2, 2};
    vector<int> ans = majorityElement(nums);

    for (int x : ans) cout << x << " ";
    cout << endl; // expected: 1 2
    return 0;
}
```

The Oracle revealed two rulers:

-   **1** spoke more than a third
-   **2** spoke more than a third

All others were silenced by balance.

---

### 🧠 Memory of the Twin-Throne Law

-   Elements appearing > ⌊n / 3⌋ → at most **2**
-   Use **two candidates and two counters**
-   First pass → cancellation logic
-   Second pass → verification
-   **Time:** O(n)
-   **Space:** O(1)

Thus is remembered the saga of **Majority Element II**,
where the Oracle refines the Rite of Balance,
allowing not one,
but **two rightful rulers**
to rise from the voices of the many. 👑👑✨
