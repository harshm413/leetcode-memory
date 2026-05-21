## 🃏📏 _The Consecutive Groups: The Hand of Straights Saga_

> \_"The Oracle was given a hand of cards --
> an array of integers.
>
> She was commanded:
>
> **'Can you rearrange the cards into groups
> where each group has exactly `groupSize` consecutive cards?'**
>
> For hand `[1, 2, 3, 6, 2, 3, 4, 7, 8]` with groupSize = 3:
> -   Group 1: [1, 2, 3] ✓
> -   Group 2: [2, 3, 4] ✓
> -   Group 3: [6, 7, 8] ✓
> -   All cards used. Possible!
>
> The Oracle's greedy strategy:
>
> **Always start a group from the SMALLEST available card.**
>
> Sort the cards (or use a frequency map).
> Find the smallest card. Try to form a consecutive group
> of size `groupSize` starting from it.
> If any card in the sequence is missing → impossible.
> If all cards are consumed → possible.
>
> Why start from the smallest?
> The smallest card MUST be the start of some group.
> It can't be in the middle or end of a group
> (nothing smaller exists to precede it).
> So it must start a group. Greedy choice is forced."\_

---

This is the saga of **Hand of Straights** (also known as **Divide Array in Groups of Consecutive Numbers**).

Given an array `hand` and an integer `groupSize`:

-   Return `true` if the array can be divided into groups
    of `groupSize` consecutive numbers.

```
Input:  hand = [1, 2, 3, 6, 2, 3, 4, 7, 8], groupSize = 3
Output: true   ([1,2,3], [2,3,4], [6,7,8])

Input:  hand = [1, 2, 3, 4, 5], groupSize = 4
Output: false  (5 cards can't be divided into groups of 4)

Input:  hand = [1, 2, 3, 4, 5, 6], groupSize = 2
Output: true   ([1,2], [3,4], [5,6])
```

---

## 🧠 The Oracle's Core Insight -- Smallest Card Must Start a Group

**Quick check:** if `n % groupSize != 0` → impossible (can't divide evenly).

**Greedy:** the smallest available card has no choice --
it MUST be the start of a group (nothing smaller can precede it).

```
1. Count frequency of each card.
2. Sort unique cards (or use an ordered map).
3. For each card (smallest first):
   If its count > 0:
     Try to form a group: card, card+1, card+2, ..., card+groupSize-1.
     For each value in the group:
       If frequency == 0 → impossible (card missing).
       Else → decrement frequency.
4. All cards consumed → true.
```

```
Time:  O(n log n) -- sorting or ordered map
Space: O(n) -- frequency map
```

---

### 📜 The Scroll of the Consecutive Groups

```cpp
#include <iostream>
#include <vector>
#include <map>
using namespace std;
```

---

## 🃏 The Greedy Ritual

```cpp
bool isNStraightHand(vector<int>& hand, int groupSize) {
    int n = hand.size();
```

---

### 🛑 Quick Check -- Divisibility

```cpp
    if (n % groupSize != 0) return false;
```

If the total cards aren't divisible by groupSize,
we can't form equal groups. Impossible immediately.

---

### 📊 Build Frequency Map (Ordered)

```cpp
    map<int, int> freq;
    for (int card : hand) {
        freq[card]++;
    }
```

`map` (not `unordered_map`) keeps keys sorted.
This gives us cards in ascending order automatically.

> _"The ordered map arranges the cards from smallest to largest.
> The smallest card is always at the front --
> ready to start the next group."_

---

### 🔁 Process Each Card (Smallest First)

```cpp
    for (auto& [card, count] : freq) {
        if (count == 0) continue;
```

Walk through cards in sorted order.
If a card's count is already 0 (used up by previous groups), skip it.

---

### 🃏 Form Groups Starting from This Card

```cpp
        int groups = count;
```

This card appears `count` times.
Each occurrence must START a group (it's the smallest available).
So we need to form `count` groups starting from this card.

---

### 📏 Build Each Consecutive Sequence

```cpp
        for (int i = 0; i < groupSize; i++) {
            if (freq[card + i] < groups) {
                return false;
            }
            freq[card + i] -= groups;
        }
    }
    return true;
}
```

For each value in the consecutive sequence `[card, card+1, ..., card+groupSize-1]`:
-   Check if enough copies exist (`freq[card+i] >= groups`).
-   If not → impossible. A required card is missing.
-   If yes → consume `groups` copies of each.

**Why consume `groups` copies at once?**

If `card` appears 3 times, we need 3 groups starting from `card`.
Each group needs one copy of `card`, `card+1`, `card+2`, etc.
So we need at least 3 copies of each value in the sequence.

> _"The smallest card forces the issue.
> It appears `count` times → `count` groups must start here.
> Each group demands one copy of every consecutive value.
> If any value falls short → the hand cannot be arranged."_

---

### 🎺 The Trial of the Consecutive Groups

```cpp
int main() {
    vector<int> h1 = {1, 2, 3, 6, 2, 3, 4, 7, 8};
    cout << isNStraightHand(h1, 3) << endl; // expected: 1 (true)

    vector<int> h2 = {1, 2, 3, 4, 5};
    cout << isNStraightHand(h2, 4) << endl; // expected: 0 (false)

    vector<int> h3 = {1, 2, 3, 4, 5, 6};
    cout << isNStraightHand(h3, 2) << endl; // expected: 1 (true)

    return 0;
}
```

---

**Full trace for hand = [1,2,3,6,2,3,4,7,8], groupSize = 3:**

**Frequency map (ordered):** `{1:1, 2:2, 3:2, 4:1, 6:1, 7:1, 8:1}`

n=9, groupSize=3. 9 % 3 == 0 ✓.

| Card | Count (groups) | Need card, card+1, card+2 | Enough? | freq after |
|------|----------------|---------------------------|---------|------------|
| 1    | 1              | Need 1×{1,2,3}            | freq[1]=1✓, freq[2]=2✓, freq[3]=2✓ | {1:0, 2:1, 3:1, 4:1, 6:1, 7:1, 8:1} |
| 2    | 1              | Need 1×{2,3,4}            | freq[2]=1✓, freq[3]=1✓, freq[4]=1✓ | {1:0, 2:0, 3:0, 4:0, 6:1, 7:1, 8:1} |
| 3    | 0              | Skip.                     | —       | — |
| 4    | 0              | Skip.                     | —       | — |
| 6    | 1              | Need 1×{6,7,8}            | freq[6]=1✓, freq[7]=1✓, freq[8]=1✓ | {all zeros} |

All cards consumed. **Answer: true** ✓

Groups formed: [1,2,3], [2,3,4], [6,7,8].

---

**Trace for hand = [1,2,3,4,5], groupSize = 4:**

n=5, groupSize=4. 5 % 4 = 1 ≠ 0. **Return false immediately.** ✓

---

**Trace for hand = [1,2,3,4,5,6], groupSize = 2:**

Freq: `{1:1, 2:1, 3:1, 4:1, 5:1, 6:1}`. n=6, 6%2=0 ✓.

| Card | Groups | Need {card, card+1} | Result |
|------|--------|---------------------|--------|
| 1    | 1      | {1,2}: 1✓, 1✓      | freq: {1:0,2:0,3:1,4:1,5:1,6:1} |
| 2    | 0      | Skip.               | — |
| 3    | 1      | {3,4}: 1✓, 1✓      | freq: {3:0,4:0,5:1,6:1} |
| 4    | 0      | Skip.               | — |
| 5    | 1      | {5,6}: 1✓, 1✓      | freq: all zeros |

**Answer: true** ✓ — groups [1,2], [3,4], [5,6].

---

**Trace for hand = [1,1,2,2,3,3], groupSize = 3:**

Freq: `{1:2, 2:2, 3:2}`. n=6, 6%3=0 ✓.

| Card | Groups | Need 2×{1,2,3} | Result |
|------|--------|----------------|--------|
| 1    | 2      | freq[1]=2✓, freq[2]=2✓, freq[3]=2✓ | All become 0. |

**Answer: true** ✓ — two groups of [1,2,3].

---

**Trace for hand = [1,2,3,4,5,6,7,8], groupSize = 3:**

Freq: `{1:1,...,8:1}`. n=8, 8%3 = 2 ≠ 0. **Return false.** ✓

---

## 🔍 Why the Smallest Card MUST Start a Group

Consider the smallest card `x` in the hand.
It can't be in the MIDDLE of a group (that would require `x-1` to exist, but `x` is the smallest).
It can't be at the END of a group (that would require `x-2` and `x-1`).
It MUST be at the START.

This is a **forced greedy choice** -- no alternative exists.
Process it first, then the next smallest becomes the new forced start.

---

## 🔍 Why Process `count` Groups at Once

If card `x` appears 3 times, ALL 3 copies must start a group.
(None can be in the middle/end of another group -- `x` is the smallest available.)

So we need 3 copies of `x+1`, 3 copies of `x+2`, etc.
Processing all 3 groups at once is cleaner than one-by-one.

---

## 🔍 Alternative -- Process One Group at a Time

```cpp
for (auto& [card, count] : freq) {
    while (count > 0) {
        for (int i = 0; i < groupSize; i++) {
            if (freq[card + i] == 0) return false;
            freq[card + i]--;
        }
    }
}
```

Form one group at a time (decrement by 1 each time).
Same result, slightly less efficient but more intuitive.

---

## 🔍 Connection to Other Problems

| Problem              | Grouping rule                      |
| -------------------- | ---------------------------------- |
| **Hand of Straights (this)** | Groups of `k` consecutive numbers |
| Divide Array (LC 1296) | Same problem, different name       |
| Task Scheduler       | Groups with cooldown constraints   |

---

### 🧠 Memory of the Consecutive Groups Law

-   **Quick check:** `n % groupSize != 0` → false
-   **Ordered frequency map** (sorted keys)
-   **For each card (smallest first):** if count > 0, form `count` groups
-   **Each group:** needs `card, card+1, ..., card+groupSize-1`
-   **If any value in sequence has insufficient count** → false
-   **Why smallest first?** It MUST start a group (nothing smaller exists)
-   **Process `count` groups at once** (all copies of smallest must start groups)
-   **Time:** O(n log n). **Space:** O(n).
-   **Edge cases:**
    -   n not divisible by groupSize → false
    -   groupSize = 1 → always true (each card is its own group)
    -   All same cards → false (unless groupSize = 1)
    -   Already consecutive → true

Thus is remembered the saga of **Hand of Straights**,
where the Oracle sorted the cards by value
and always started groups from the smallest available --
knowing it had no choice but to lead --
consuming consecutive sequences greedily,
checking that enough copies existed for each value --
until every card found its group
or a missing card declared the hand impossible. 🃏📏✨
