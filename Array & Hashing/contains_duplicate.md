## 🕯️👥 _The Tale of the Mirror Shadows: The Contains Duplicate Saga_

> \_"Not every face in the crowd was unique.
> Some were echoes — shadows wearing another's skin.
> To find them, you needed to look beyond the surface,
> into the Book of Seen Faces.
>
> The Oracle was tasked with a single question:
>
> **'Does this crowd contain any duplicates?
> Is there even a single number
> that appears more than once?'**
>
> She did not need to find all duplicates.
> She did not need to count them.
> She only needed to find ONE —
> and declare the truth.
>
> The Oracle opened the **Book of Seen Faces** —
> an unordered set that remembered
> every traveler it had ever encountered.
>
> Then she walked the crowd.
>
> For each traveler, she asked the Book:
> 'Have you seen this face before?'
>
> If YES — a shadow had been found.
> Declare TRUE and stop.
>
> If NO — record this traveler in the Book
> so their twin would be caught
> if they ever arrived.
>
> If the entire crowd passed through
> and no shadow was found —
> declare FALSE.
>
> The crowd was unique."\_

---

This is the saga of **Contains Duplicate**.

You are given an integer array `nums`.

Return `true` if any value appears **at least twice**.
Return `false` if every element is **distinct**.

```
Input:  [1, 2, 3, 1]  →  Output: true   (1 appears twice)
Input:  [1, 2, 3, 4]  →  Output: false  (all distinct)
Input:  [1, 1, 1, 3, 3, 4, 3, 2, 4, 2]  →  Output: true
```

---

## 🧠 The Oracle's Core Insight — The Book of Seen Faces

The entire algorithm reduces to one question asked N times:

```
"Have I seen this number before?"
```

An **unordered_set** answers this in O(1) average time.

```
For each number:
  If it's already in the set → duplicate found → return true
  Else → insert it into the set → continue

If the loop ends without finding a duplicate → return false
```

There is no sorting, no nested loop, no counting.
One pass. One set. Immediate detection.

**Why not sort first?**

Sorting would work — duplicates become adjacent after sorting —
but it costs O(N log N) and modifies the input.

The set approach is O(N) time and O(N) space —
faster, non-destructive.

---

### 📜 The Scroll of Witnesses

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;
```

The scribes opened their scrolls (`iostream`)
and gathered the line of travelers (`vector`).

The **Book of Seen Faces** was summoned — the `unordered_set`.
It remembered every traveler it had met.
If ever a shadow tried to step in with the same banner —
the book cried out instantly.

---

## 🕵️ The Oracle's Ritual

```cpp
bool containsDuplicate(vector<int>& nums) {
```

The Oracle began her ritual,
invoking **containsDuplicate**.
A wave spread across the crowd —
all banners would now be inspected.

---

```cpp
    unordered_set<int> seen;
```

The **Book of Seen Faces** was laid open.
Its pages were blank — eager to record the first banner that passed.

---

## 👣 The March of Travelers

```cpp
    for (int x : nums) {
```

One by one, travelers entered the square.
Their banners (`x`) gleamed under the sun,
each demanding recognition.

---

### 🪞 The Test of Shadows

```cpp
        if (seen.count(x)) {
            return true;
        }
```

The Oracle leaned over the book.

If the banner `x` was already etched upon its pages —
this traveler was no unique soul.
They were a **shadow** — a duplicate.

The ritual halted at once, declaring `true` —
for the land had seen this face before.

> _"The moment a shadow is caught,
> there is no need to inspect the rest of the crowd.
> One duplicate is enough.
> The answer is known."_

---

```cpp
        seen.insert(x);
    }
```

But if the banner was new —
the traveler was welcomed.
Their identity was written into the **Book of Seen Faces** —
ensuring they would be recognized
if ever a shadow tried to mimic them later.

---

## 🌅 The End of the March

```cpp
    return false;
}
```

When the last traveler had passed
and no shadows were found —
the Oracle closed the book.

She declared `false` —
no duplicates lurked in this crowd.

Every face was unique.
Peace returned to the market square.

---

### 🎺 The Trial of Five

```cpp
int main() {
    vector<int> nums1 = {1, 2, 3, 1};
    cout << (containsDuplicate(nums1) ? "true" : "false") << endl; // true

    vector<int> nums2 = {1, 2, 3, 4};
    cout << (containsDuplicate(nums2) ? "true" : "false") << endl; // false

    vector<int> nums3 = {1, 1, 1, 3, 3, 4, 3, 2, 4, 2};
    cout << (containsDuplicate(nums3) ? "true" : "false") << endl; // true

    return 0;
}
```

**Trace for `[1, 2, 3, 1]`:**

| Step | x   | seen before? | Action          | seen set |
| ---- | --- | ------------ | --------------- | -------- |
| 1    | 1   | No           | insert 1        | {1}      |
| 2    | 2   | No           | insert 2        | {1,2}    |
| 3    | 3   | No           | insert 3        | {1,2,3}  |
| 4    | 1   | **YES**      | return **true** | —        |

The shadow of `1` was caught at step 4.
The Oracle declared: **true** ✓

**Trace for `[1, 2, 3, 4]`:**

| Step | x   | seen before? | Action   | seen set  |
| ---- | --- | ------------ | -------- | --------- |
| 1    | 1   | No           | insert 1 | {1}       |
| 2    | 2   | No           | insert 2 | {1,2}     |
| 3    | 3   | No           | insert 3 | {1,2,3}   |
| 4    | 4   | No           | insert 4 | {1,2,3,4} |

Loop ends. Return **false** ✓

Every traveler was unique. The Book recorded four distinct faces.

---

### 🧠 Memory of the Mirror Shadows Law

-   **`unordered_set<int> seen`** → the Book of Seen Faces
-   **`seen.count(x)`** → check if x was seen before — O(1) average
-   **`seen.insert(x)`** → record the new traveler — O(1) average
-   **If found in set** → `return true` immediately — stop the march
-   **If loop ends** → `return false` — no duplicates in the crowd
-   Early return is the key efficiency — stop the moment a duplicate is found
-   **Time:** O(N) average — one pass, each lookup and insert is O(1)
-   **Space:** O(N) — the set grows to hold at most N distinct elements

Thus is remembered the saga of **Contains Duplicate**,
where shadows reveal themselves
only to those who carry the Book of Memory —
the Oracle recording every traveler she met,
stopping the instant a face appeared twice,
and declaring peace only after every last face
had passed through the square
without finding its twin. 🕯️👥✨
