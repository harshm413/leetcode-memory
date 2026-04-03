## 👑👑 _The Twin Thrones of the Assembly: The Majority Element II Saga_

> _"In the Great Assembly of Numbers,
> voices rose once more —
> but this time, no single ruler could dominate alone.
>
> The ancient law had changed:
>
> **'Only those who speak more than one-third of the time
> may claim a throne.'**
>
> The Oracle knew a mathematical truth:
>
> **At most TWO elements can appear more than ⌊n/3⌋ times.**
>
> Why? Because if three elements each appeared more than n/3 times,
> their total count would exceed n — impossible.
>
> So the Oracle refined the Rite of Balance —
> the Boyer-Moore Voting Algorithm —
> to track **two candidates** instead of one.
>
> The ritual had two phases:
>
> **Phase 1 — The Voting:**
> Maintain two candidates and two counters.
> When a voice matches a candidate, strengthen them.
> When a throne is empty, crown the new voice.
> When a voice opposes both, cancel both counters.
>
> **Phase 2 — The Verification:**
> Count the true occurrences of both candidates.
> Only those appearing more than ⌊n/3⌋ times
> are declared true rulers.
>
> Two passes. O(1) space. Two thrones revealed."_

---

This is the saga of **Majority Element II**.

You are given an array `nums`.

Your task:

- Find **all elements** that appear **more than ⌊n / 3⌋ times**.
- Return them in **any order**.

```
Input:  [3, 2, 3]
Output: [3]

Input:  [1, 1, 1, 3, 3, 2, 2, 2]
Output: [1, 2]

Input:  [1]
Output: [1]
```

---

## 🧠 The Oracle's Core Insight — At Most Two Rulers

The key mathematical insight:

> **At most TWO elements can appear more than ⌊n/3⌋ times.**

Proof by contradiction:

```
Suppose three elements A, B, C each appear more than n/3 times.
Then: count(A) + count(B) + count(C) > n/3 + n/3 + n/3 = n
But the total count of all elements is exactly n.
Contradiction!
```

So we only need to track **two candidates**.

The algorithm is an extension of Boyer-Moore Voting:

```
Phase 1 — Voting (find two candidates):
  Maintain cand1, cand2, cnt1, cnt2
  For each element:
    If matches cand1 → increment cnt1
    Else if matches cand2 → increment cnt2
    Else if cnt1 == 0 → crown as cand1
    Else if cnt2 == 0 → crown as cand2
    Else → cancel both counters (decrement both)

Phase 2 — Verification (count actual occurrences):
  Count how many times cand1 and cand2 actually appear
  Return only those with count > n/3
```

**Time:** O(N) — two passes
**Space:** O(1) — only 4 variables

---

### 📜 The Scroll of the Divided Assembly

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚖️ The Oracle's Two-Throne Voting Ritual

```cpp
vector<int> majorityElement(vector<int>& nums) {
    int cand1 = 0, cand2 = 0;
    int cnt1 = 0, cnt2 = 0;
```

At first, both thrones were empty.

`cand1` and `cand2` — the two potential rulers.
`cnt1` and `cnt2` — their respective support counters.

Both started at 0 — no ruler yet claimed authority.

---

## 🗣️ Phase 1 — The Rite of Balance (Voting)

```cpp
    for (int x : nums) {
```

The Oracle walked through every voice in the assembly.

---

```cpp
        if (x == cand1) {
            cnt1++;
        } else if (x == cand2) {
            cnt2++;
        }
```

**If the voice matched an existing ruler,**
their support grew stronger.

Note: we check `cand1` first, then `cand2`.
This ensures that if `cand1 == cand2` (both empty initially),
we don't double-count.

---

## 👑 Crown New Rulers if a Throne Is Empty

```cpp
        else if (cnt1 == 0) {
            cand1 = x;
            cnt1 = 1;
        } else if (cnt2 == 0) {
            cand2 = x;
            cnt2 = 1;
        }
```

**If a throne stood empty** (`cnt == 0`),
the current voice was crowned as the new ruler.

The counter was set to 1 — one supporter.

---

## ⚔️ Cancel Opposing Voices

```cpp
        else {
            cnt1--;
            cnt2--;
        }
    }
```

**When a voice matched neither ruler,**
it opposed both.

Balance was restored through cancellation —
both counters decreased by 1.

> _"A voice that challenges both thrones
> weakens both rulers equally."_

---

## 🔍 Phase 2 — The Trial of Verification

After the voting phase, `cand1` and `cand2` are **candidates** —
but not yet confirmed rulers.

The Oracle knew better than to trust appearances alone.

```cpp
    cnt1 = 0;
    cnt2 = 0;
```

She reset the counters to zero.

---

## 📊 Count the True Strength of Each Candidate

```cpp
    for (int x : nums) {
        if (x == cand1) cnt1++;
        else if (x == cand2) cnt2++;
    }
```

Now the true voices were counted honestly —
no cancellation, no tricks.

Just a straightforward count of how many times
each candidate actually appeared.

---

## 🏆 Declare the True Rulers

```cpp
    vector<int> res;
    int n = nums.size();
    if (cnt1 > n / 3) res.push_back(cand1);
    if (cnt2 > n / 3) res.push_back(cand2);
    return res;
}
```

Only those who spoke **more than ⌊n / 3⌋ times**
were allowed to keep their thrones.

If `cand1` qualified, it was added to the result.
If `cand2` qualified, it was added to the result.

If neither qualified — the result was empty.
If both qualified — both were returned.

---

### 🎺 The Trial of the Dual Rulers

```cpp
int main() {
    vector<int> nums1 = {3, 2, 3};
    vector<int> ans1 = majorityElement(nums1);
    for (int x : ans1) cout << x << " ";
    cout << endl; // expected: 3

    vector<int> nums2 = {1, 1, 1, 3, 3, 2, 2, 2};
    vector<int> ans2 = majorityElement(nums2);
    for (int x : ans2) cout << x << " ";
    cout << endl; // expected: 1 2

    return 0;
}
```

---

**Trace for `[1, 1, 1, 3, 3, 2, 2, 2]` (n=8, threshold=8/3=2):**

**Phase 1 — Voting:**

| x | cand1 | cnt1 | cand2 | cnt2 | Action                          |
|---|-------|------|-------|------|---------------------------------|
| 1 | 0     | 0    | 0     | 0    | cnt1==0 → cand1=1, cnt1=1       |
| 1 | 1     | 1    | 0     | 0    | x==cand1 → cnt1=2               |
| 1 | 1     | 2    | 0     | 0    | x==cand1 → cnt1=3               |
| 3 | 1     | 3    | 0     | 0    | cnt2==0 → cand2=3, cnt2=1       |
| 3 | 1     | 3    | 3     | 1    | x==cand2 → cnt2=2               |
| 2 | 1     | 3    | 3     | 2    | x≠both → cnt1=2, cnt2=1         |
| 2 | 1     | 2    | 3     | 1    | x≠both → cnt1=1, cnt2=0         |
| 2 | 1     | 1    | 3     | 0    | cnt2==0 → cand2=2, cnt2=1       |

After Phase 1: `cand1=1, cand2=2`

**Phase 2 — Verification:**

Count occurrences:
- `1` appears 3 times → 3 > 8/3 (2.67) → **qualified** ✓
- `2` appears 3 times → 3 > 8/3 (2.67) → **qualified** ✓

**Answer: [1, 2]** ✓

---

**Trace for `[3, 2, 3]` (n=3, threshold=3/3=1):**

**Phase 1 — Voting:**

| x | cand1 | cnt1 | cand2 | cnt2 | Action                    |
|---|-------|------|-------|------|---------------------------|
| 3 | 0     | 0    | 0     | 0    | cnt1==0 → cand1=3, cnt1=1 |
| 2 | 3     | 1    | 0     | 0    | cnt2==0 → cand2=2, cnt2=1 |
| 3 | 3     | 1    | 2     | 1    | x==cand1 → cnt1=2         |

After Phase 1: `cand1=3, cand2=2`

**Phase 2 — Verification:**

Count occurrences:
- `3` appears 2 times → 2 > 3/3 (1) → **qualified** ✓
- `2` appears 1 time → 1 ≤ 3/3 (1) → **not qualified** ✗

**Answer: [3]** ✓

---

## ⚠️ Why Verification is Necessary

The voting phase only guarantees that **if** a majority element exists,
it will be one of the two candidates.

But it doesn't guarantee the candidates are actually majority elements.

Example: `[1, 2, 3]` with threshold n/3 = 1

After voting: `cand1=1, cand2=3` (or similar)
But none of them appear more than 1 time.
Verification catches this and returns `[]`.

---

### 🧠 Memory of the Twin-Throne Law

- **At most TWO elements** can appear more than ⌊n/3⌋ times
- **Phase 1 — Voting:** maintain two candidates and two counters
  - If x matches cand1 → increment cnt1
  - Else if x matches cand2 → increment cnt2
  - Else if cnt1 == 0 → crown x as cand1
  - Else if cnt2 == 0 → crown x as cand2
  - Else → cancel both (decrement both counters)
- **Phase 2 — Verification:** count actual occurrences
  - Only return candidates with count > n/3
- **Time:** O(N) — two passes through the array
- **Space:** O(1) — only 4 variables (cand1, cand2, cnt1, cnt2)
- **Edge cases:**
  - No majority element → return empty array
  - One majority element → return array with one element
  - Two majority elements → return array with both

Thus is remembered the saga of **Majority Element II**,
where the Oracle refined the Rite of Balance
to track not one, but **two rightful rulers** —
using cancellation to find candidates,
and verification to confirm their true strength —
revealing all voices that spoke more than one-third of the time. 👑👑✨
