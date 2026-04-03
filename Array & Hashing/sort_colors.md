## 🎨⚔️ _The Three Banners of the Kingdom: The Sort Colors Saga_

> \_"The Kingdom's battlefield was a mess.
> Soldiers of three allegiances stood in random order —
> those bearing the **Red banner** (0),
> those bearing the **White banner** (1),
> and those bearing the **Blue banner** (2).
>
> The General commanded:
>
> **'Sort them — Red on the left,
> White in the middle,
> Blue on the right.
> And do it in a single pass.
> No counting. No extra arrays.'**
>
> The naive soldier would count the reds, whites, and blues —
> then overwrite the array.
> Correct — but two passes.
>
> The wise General knew a more elegant way:
> the **Dutch National Flag Algorithm**,
> conceived by the great Dijkstra himself.
>
> Three pointers would divide the battlefield
> into four zones at any moment:
>
> Everything left of `low` — confirmed Red (0).
> Everything between `low` and `mid` — confirmed White (1).
> Everything between `mid` and `high` — unknown, unsorted.
> Everything right of `high` — confirmed Blue (2).
>
> The `mid` pointer was the frontier soldier —
> always standing at the boundary of the unknown zone,
> marching forward and making decisions
> one soldier at a time.
>
> Three cases. One pass. In-place. Done."\_

---

This is the saga of **Sort Colors**.

You are given an array `nums` with values `0`, `1`, or `2`
representing red, white, and blue.

Your task:

-   Sort the array **in-place** so all `0`s come first, then `1`s, then `2`s.
-   You must do it in **O(N) time** using **O(1) extra space**.
-   **One pass only** (no counting sort).

```
Input:  [2, 0, 2, 1, 1, 0]
Output: [0, 0, 1, 1, 2, 2]

Input:  [2, 0, 1]
Output: [0, 1, 2]
```

---

## 🧠 The Oracle's Core Insight — Dutch National Flag: Three Zones, Three Pointers

At any point during the algorithm, the array is divided into four regions:

```
[0, 0, ..., 0 | 1, 1, ..., 1 | ? ? ? ? ? | 2, 2, ..., 2]
               ↑              ↑            ↑
              low            mid          high
```

-   **`[0, low)` indices:** all confirmed `0`s (Red) — fully sorted.
-   **`[low, mid)` indices:** all confirmed `1`s (White) — fully sorted.
-   **`[mid, high]` indices:** unknown — not yet inspected.
-   **`(high, end)` indices:** all confirmed `2`s (Blue) — fully sorted.

`mid` is the explorer — it moves through the unknown zone.

**Three decisions `mid` makes:**

```
Case 1: nums[mid] == 0 (Red)
   → Swap nums[mid] with nums[low]
   → low++, mid++  (both zones grow rightward)

Case 2: nums[mid] == 1 (White)
   → Already in the right zone (between low and mid)
   → Just advance mid++ (expand the white zone)

Case 3: nums[mid] == 2 (Blue)
   → Swap nums[mid] with nums[high]
   → high--  (blue zone grows leftward)
   → Do NOT advance mid (the swapped element from high is still unknown)
```

The loop ends when `mid > high` — the unknown zone is empty.

---

### 📜 The Scroll of the Three Banners

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

---

## 🏳️ The Three Pointers Take Their Positions

```cpp
void sortColors(vector<int>& nums) {
    int low = 0;
    int mid = 0;
    int high = nums.size() - 1;
```

Three generals took the field:

-   `low` — the **Red frontier** — left boundary of the white zone.
    Everything to its left was confirmed Red.

-   `mid` — the **Explorer** — the frontier of the unknown.
    He marched forward, inspecting each soldier and deciding their fate.

-   `high` — the **Blue frontier** — right boundary of the unknown zone.
    Everything to its right was confirmed Blue.

At the start — the entire array was unknown.
`low` and `mid` stood at `0`, `high` at the last index.

---

## 🔁 The March of the Explorer

```cpp
    while (mid <= high) {
```

The Explorer marched as long as the unknown zone was non-empty —
as long as `mid` had not crossed past `high`.

---

### 🔴 Case 1 — Red Soldier at the Frontier

```cpp
        if (nums[mid] == 0) {
            swap(nums[mid], nums[low]);
            low++;
            mid++;
        }
```

The Explorer found a Red soldier (`0`) at `mid`.

He swapped it with the soldier at `low` —
sending the Red soldier to join their confirmed brethren on the left.

The soldier who came back from `low` was a White (`1`) —
because everything between `low` and `mid` was already confirmed White.
The newly arrived White was now correctly sitting at `mid-1` after `mid++`.

Both `low` and `mid` advanced:

-   `low++` because the red zone grew by one.
-   `mid++` because the swapped-in element is confirmed White — already processed.

---

### ⚪ Case 2 — White Soldier at the Frontier

```cpp
        else if (nums[mid] == 1) {
            mid++;
        }
```

The Explorer found a White soldier (`1`) at `mid`.

White soldiers belong exactly where they are —
between `low` and `mid`.

No swap needed. Simply advance `mid` to expand the white zone.

---

### 🔵 Case 3 — Blue Soldier at the Frontier

```cpp
        else {
            swap(nums[mid], nums[high]);
            high--;
        }
    }
}
```

The Explorer found a Blue soldier (`2`) at `mid`.

He swapped it with the soldier at `high` —
sending the Blue soldier to join their brethren on the right.

`high--` shrank the unknown zone from the right.

**Crucially — `mid` did NOT advance.**

Why? The soldier who came from `high` was unknown —
it had not been inspected yet.
It might be Red, White, or Blue.
The Explorer must examine it in the next iteration
before moving on.

> _"A soldier arriving from the right carries no guarantees.
> The Explorer must look them in the eye
> before deciding their fate."_

---

### 🎺 The Trial of the Three Banners

```cpp
int main() {
    vector<int> nums1 = {2, 0, 2, 1, 1, 0};
    sortColors(nums1);
    for (int x : nums1) cout << x << " ";
    cout << endl; // 0 0 1 1 2 2

    vector<int> nums2 = {2, 0, 1};
    sortColors(nums2);
    for (int x : nums2) cout << x << " ";
    cout << endl; // 0 1 2

    return 0;
}
```

**Full trace for `[2, 0, 2, 1, 1, 0]`:**

Initial: `low=0, mid=0, high=5`

| mid | nums[mid] | Action                 | Array             | low | mid | high |
| --- | --------- | ---------------------- | ----------------- | --- | --- | ---- |
| 0   | 2 (Blue)  | swap(0,5), high--      | [0,0,2,1,1,**2**] | 0   | 0   | 4    |
| 0   | 0 (Red)   | swap(0,0), low++,mid++ | [**0**,0,2,1,1,2] | 1   | 1   | 4    |
| 1   | 0 (Red)   | swap(1,1), low++,mid++ | [0,**0**,2,1,1,2] | 2   | 2   | 4    |
| 2   | 2 (Blue)  | swap(2,4), high--      | [0,0,**1**,1,2,2] | 2   | 2   | 3    |
| 2   | 1 (White) | mid++                  | [0,0,1,1,2,2]     | 2   | 3   | 3    |
| 3   | 1 (White) | mid++                  | [0,0,1,1,2,2]     | 2   | 4   | 3    |

`mid(4) > high(3)` → **stop**.

Result: `[0, 0, 1, 1, 2, 2]` ✓

---

### 🧠 Memory of the Three Banner Law

-   **Three pointers:** `low` (red frontier), `mid` (explorer), `high` (blue frontier)
-   **Loop condition:** `while (mid <= high)` — stop when unknown zone is empty
-   **nums[mid] == 0:** swap with `low`, advance **both** `low++` and `mid++`
-   **nums[mid] == 1:** advance only `mid++` — white stays where it is
-   **nums[mid] == 2:** swap with `high`, advance only `high--` — **do NOT advance mid**
-   The reason mid doesn't move on a Blue swap: the element arriving from `high` is still unknown and must be re-examined
-   **Time:** O(N) — one single pass, every element processed at most once
-   **Space:** O(1) — only three pointer variables, in-place sorting

Thus is remembered the saga of **Sort Colors**,
where the General divided the battlefield into four zones
with three pointers —
sending Red soldiers left, Blue soldiers right,
and letting White soldiers settle peacefully in the middle —
all in a single fearless march
from one end of the battlefield to the other. 🎨⚔️✨
