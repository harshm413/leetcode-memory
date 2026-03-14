## 🎭🎯 _The Impostor Knights of the Twisted Kingdom: The Search in Rotated Sorted Array II Saga_

> \_"The Twisted Kingdom had been searched before.
> The Seeker had learned its ways —
> always one half obeys the old order,
> and the other curves strangely.
>
> But the King, cunning and devious,
> had now filled the kingdom with **Impostor Knights** —
> soldiers bearing the same banners
> as those who guarded the borders.
>
> When the Seeker looked at the edges
> and peered at the middle,
> he could no longer trust what he saw.
>
> **Were the borders and the middle
> wearing the same banner
> because the sorted half was there —
> or merely because an impostor stood in disguise?**
>
> The Seeker could not know.
>
> So he devised a new law:
>
> **'When the border and the middle
> wear the same face —
> strip one impostor from each side
> and look again.'**
>
> The kingdom would shrink,
> the disguise would fall,
> and truth would stand revealed."\_

---

This is the saga of **Search in Rotated Sorted Array II**.

You are given:

-   a rotated sorted array `nums` — **possibly containing duplicates**
-   a target value `target`

Your task:

-   Return `true` if `target` exists.
-   Otherwise `false`.

---

## 🧠 The Oracle's Core Problem — Duplicates Break the Old Law

In the original kingdom (no duplicates),
the Seeker could always tell which half was sorted
by comparing the left border with the middle:

```
nums[l] <= nums[mid]  →  left half is sorted
```

But now with impostors (duplicates), consider:

```
nums = {1, 1, 1, 3, 1}
l = 0, r = 4, mid = 2
nums[l] = 1, nums[mid] = 1
```

Both borders wear the same banner as the middle.

Is the left half sorted? Is the right?
**The Seeker cannot tell.**

So he invented the **Impostor Stripping Rule**:

> When `nums[l] == nums[mid] == nums[r]`,
> strip one soldier from each end:
> `l++` and `r--`

And try again with a smaller, cleaner kingdom.

---

### 📜 The Seeker's Compass and Map

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

The same scrolls from the first quest.
But now the kingdom held shadows of itself — duplicates lurking at every corner.

---

## 🗡️ The Search Ritual — Rotated Binary Search with Impostor Detection

```cpp
bool search(vector<int>& nums, int target) {
    int l = 0, r = nums.size() - 1;
```

The Seeker planted his feet at both ends of the realm,
just as before.

---

```cpp
    while (l <= r) {
        int mid = l + (r - l) / 2;
```

He split the kingdom at the middle
and gazed upon the banner there.

---

### 🎯 Step 1 — Is the Knight Standing Right Here?

```cpp
        if (nums[mid] == target) return true;
```

If the knight's banner matched the middle exactly —
the quest was over.
No need to look further.

---

### 🎭 Step 2 — Unmask the Impostors First

```cpp
        if (nums[l] == nums[mid] && nums[mid] == nums[r]) {
            l++;
            r--;
        }
```

This is the **crucial new scene** that did not exist before.

The Seeker looked at the left border, the middle, and the right border.

If **all three wore the same mask** —
he could not determine which half was sorted.
The impostors had created perfect confusion.

So he simply **stripped one soldier from each flank**
and shrank the kingdom by one on both sides.

No information was lost — the target could not hide
in a border soldier who was merely a copy.

---

### ⚔️ Step 3 — The Law of the Straight Half (Left Sorted)

```cpp
        else if (nums[l] <= nums[mid]) {
```

Now, with impostors stripped away,
if the left border was still smaller than or equal to the middle,
the **left half obeyed the Old Law of Sortedness**.

---

```cpp
            if (nums[l] <= target && target < nums[mid]) {
                r = mid - 1;
            } else {
                l = mid + 1;
            }
```

If the target's banner fell within the left half's clean, ordered range —
ride left.

Otherwise, the knight hid somewhere in the twisted right.
Ride right.

---

### 🔄 Step 4 — The Twisted Right Half Must Be Sorted

```cpp
        } else {
            if (nums[mid] < target && target <= nums[r]) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
    }
```

If the left half was not sorted,
by the kingdom's nature the **right half must be**.

The same logic applied in reverse:
If the target lay within the right half's clean range — ride right.
Otherwise, the knight lurked in the twisted left.

Step by step, impostor by impostor, half by half —
the Seeker closed in.

---

### 🏁 If the Knight Was Never Found

```cpp
    return false;
}
```

When the search space collapsed entirely
and no banner had matched —
the knight did not exist in this kingdom.

---

### 🎺 The Trial of the Impostor Kingdom

```cpp
int main() {
    vector<int> nums1 = {2, 5, 6, 0, 0, 1, 2};
    cout << search(nums1, 0) << endl; // true

    vector<int> nums2 = {2, 5, 6, 0, 0, 1, 2};
    cout << search(nums2, 3) << endl; // false

    return 0;
}
```

In the first trial:
The kingdom `{2,5,6,0,0,1,2}` hid the knight `0` among duplicates.
The Seeker stripped the border impostors (`2` on both ends),
identified the sorted half,
and found the knight at position `3`.

In the second trial:
No knight bearing banner `3` existed anywhere.
The Seeker exhausted the search — and returned `false`.

---

### 🧠 Memory of the Impostor Kingdom Law

-   Same as Rotated Binary Search I — but with **one new scene**
-   **The new scene:** when `nums[l] == nums[mid] == nums[r]` → do `l++, r--`
-   This strips impostors from both flanks when identity cannot be determined
-   After stripping, the old left/right sorted-half logic resumes exactly
-   **Best/Average Time:** O(log n)
-   **Worst Time:** O(n) — when all elements are identical impostors
-   **Space:** O(1)

Thus is remembered the saga of
**Search in Rotated Sorted Array II**,
where the Twisted Kingdom returned — this time disguised in duplicate banners.
The Seeker did not panic.
He simply learned to strip one impostor from each flank
whenever the borders and the middle wore the same face —
shrinking the deception step by step,
until the truth of the sorted half was clear again,
and the lost knight's banner
was either found — or declared never to have existed. 🎭🎯✨
