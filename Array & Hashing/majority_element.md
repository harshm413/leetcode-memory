## 👑🗳️ _The Voice of the Many: The Majority Element Saga_

> _"In the Assembly of Numbers,
> every value raised its voice.
>
> Some spoke once,
> some twice,
> but one voice thundered louder than all others combined.
>
> The Oracle was summoned with a decree:
>
> **'Find the element that appears more than half the time.'**
>
> No counting scrolls were allowed,
> no ledgers of frequency,
> only a single pass through the assembly.
>
> The Oracle knew a profound truth:
>
> **If an element appears more than n/2 times,
> it can survive any amount of cancellation
> against all other elements combined.**
>
> She invoked the ancient Rite of Balance —
> the Boyer-Moore Voting Algorithm —
> where opposing voices cancel each other
> until only the true ruler remains.
>
> The ritual was simple:
>
> Maintain a candidate and a counter.
> When the counter reaches zero, crown a new candidate.
> When a voice matches the candidate, strengthen them.
> When a voice opposes, cancel one supporter.
>
> After one pass, the candidate is the majority element.
> Guaranteed."_

---

This is the saga of **Majority Element**.

You are given an array `nums` of size `n`.

The **majority element** is the element that appears **more than ⌊n / 2⌋ times**.

You may assume that the majority element **always exists** in the array.

```
Input:  [3, 2, 3]
Output: 3

Input:  [2, 2, 1, 1, 1, 2, 2]
Output: 2
```

---

## 🧠 The Oracle's Core Insight — Cancellation Reveals the Majority

The key insight:

> **If an element appears more than n/2 times,
> it will survive pairwise cancellation with all other elements.**

Why?

```
Let majority element M appear k times, where k > n/2.
All other elements combined appear (n - k) times, where (n - k) < n/2.

If we pair each M with a non-M and cancel them:
  M survives with at least k - (n - k) = 2k - n > 0 instances.

So M can never be fully canceled.
```

The Boyer-Moore Voting Algorithm exploits this:

```
Maintain: candidate, count
For each element x:
  If count == 0 → crown x as new candidate, count = 1
  Else if x == candidate → count++
  Else → count-- (cancel one supporter)

After the loop, candidate is the majority element.
```

**Time:** O(N) — single pass
**Space:** O(1) — only two variables

---

### 📜 The Scroll of the Great Assembly

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## ⚖️ The Oracle's Rite of Balance

_Cancel the weak, preserve the ruler_

```cpp
int majorityElement(vector<int>& nums) {
    int candidate = 0;
    int count = 0;
```

At first, there was no ruler —
no voice strong enough to lead.

`candidate` — the current potential majority element.
`count` — the number of supporters minus opponents.

Both started at 0.

---

## 🗣️ Walk Through the Assembly

```cpp
    for (int x : nums) {
```

Each number stepped forward to speak.

---

## 👑 If No Leader Stands, Crown One

```cpp
        if (count == 0) {
            candidate = x;
            count = 1;
        }
```

When the counter reached zero,
the current candidate had been fully canceled.

The current speaker `x` was crowned as the new **candidate**.
The counter was reset to 1 — one supporter.

> _"When balance is lost,
> a new voice rises to claim the throne."_

---

## ⚔️ Supporters vs Opponents

```cpp
        else if (x == candidate) {
            count++;
        } else {
            count--;
        }
    }
```

**If the voice matched the current candidate:**
Support grew stronger. The counter increased.

**If the voice differed:**
Opposition arose. One supporter was canceled.
The counter decreased.

Voices clashed and neutralized each other.

> _"Every opposing voice cancels one supporter.
> But the true majority has more supporters
> than all opponents combined."_

---

## 🏆 The Survivor Is the Ruler

```cpp
    return candidate;
}
```

When the dust settled,
only the true majority could remain standing.

The `candidate` at the end is guaranteed to be
the majority element.

No verification needed —
the problem guarantees a majority element exists.

---

### 🎺 The Trial of the Loudest Voice

```cpp
int main() {
    vector<int> nums1 = {3, 2, 3};
    cout << majorityElement(nums1) << endl; // expected: 3

    vector<int> nums2 = {2, 2, 1, 1, 1, 2, 2};
    cout << majorityElement(nums2) << endl; // expected: 2

    return 0;
}
```

---

**Trace for `[2, 2, 1, 1, 1, 2, 2]` (n=7, majority threshold=3):**

| x | candidate | count | Action                          |
|---|-----------|-------|---------------------------------|
| 2 | 0         | 0     | count==0 → candidate=2, count=1 |
| 2 | 2         | 1     | x==candidate → count=2          |
| 1 | 2         | 2     | x≠candidate → count=1           |
| 1 | 2         | 1     | x≠candidate → count=0           |
| 1 | 2         | 0     | count==0 → candidate=1, count=1 |
| 2 | 1         | 1     | x≠candidate → count=0           |
| 2 | 1         | 0     | count==0 → candidate=2, count=1 |

**Final candidate: 2** ✓

Element `2` appears 4 times (more than 7/2 = 3.5).

---

**Trace for `[3, 2, 3]` (n=3, majority threshold=1):**

| x | candidate | count | Action                          |
|---|-----------|-------|---------------------------------|
| 3 | 0         | 0     | count==0 → candidate=3, count=1 |
| 2 | 3         | 1     | x≠candidate → count=0           |
| 3 | 3         | 0     | count==0 → candidate=3, count=1 |

**Final candidate: 3** ✓

Element `3` appears 2 times (more than 3/2 = 1.5).

---

## 🔍 Why This Works — The Mathematical Proof

Let's prove the algorithm is correct:

**Claim:** If a majority element M exists (appears > n/2 times),
the final candidate will be M.

**Proof:**

Consider the array as a sequence of +1 (for M) and -1 (for non-M).

Since M appears more than n/2 times:
```
count(M) > n/2
count(non-M) < n/2
```

The algorithm maintains a running "balance" (the count variable).

Every time we see M, we add 1 to the balance (if M is the candidate)
or subtract 1 (if M is not the candidate, but this means we're canceling a non-M).

Every time we see non-M, we subtract 1 from the balance (if non-M is the candidate)
or add 1 (if we're canceling a non-M with M as candidate).

The key insight: M has more instances than all non-M combined.

So even if M gets canceled by non-M elements,
M will eventually become the candidate and stay as the candidate
because it has more supporters than opponents.

---

## ⚠️ What If No Majority Element Exists?

The problem guarantees a majority element exists.

But if it didn't, the algorithm would still return a candidate —
just not necessarily a majority element.

To handle this case, you'd need a verification pass:

```cpp
int count = 0;
for (int x : nums) {
    if (x == candidate) count++;
}
if (count > nums.size() / 2) return candidate;
else return -1; // or throw exception
```

But for this problem, verification is unnecessary.

---

### 🧠 Memory of the Voting Rite

- **Majority element** appears more than ⌊n/2⌋ times
- **Boyer-Moore Voting Algorithm:** maintain candidate and count
- When count == 0 → crown new candidate
- When x == candidate → count++
- When x ≠ candidate → count--
- **Final candidate is guaranteed to be the majority** (if one exists)
- **Time:** O(N) — single pass through the array
- **Space:** O(1) — only two variables
- **Why it works:** majority element has more instances than all others combined,
  so it survives pairwise cancellation
- **Edge cases:**
  - All elements the same → that element is the majority
  - Array of size 1 → that element is the majority

Thus is remembered the saga of **Majority Element**,
where the Oracle listened not to every voice equally,
but allowed balance and cancellation
to reveal the one ruler
whose presence could never be silenced —
the voice that spoke more than half the time,
surviving every challenge,
emerging victorious from the chaos. 👑🗳️✨
