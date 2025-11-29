## 🎯 _The Lost Knight in the Twisted Kingdom: The Search in Rotated Sorted Array Saga_

> _"A kingdom once straight as an arrow was bent upon its axis.
> The knight we seek still stands somewhere within —
> neither vanished nor changed —
> but the roads curve strangely now.
> To find him, split the land, learn which half obeys the old order,
> and ride with certainty toward his banner."_

---

The kingdom of numbers had once been purely ascending — a perfect, growing line.
But then the Wheel of Rotation spun the land, breaking the sunrise and sunset apart.
Still sorted in spirit, but twisted in form, the citizens remained where they were placed.

Now the Seeker was tasked with locating a single knight — the target value — somewhere in this rotated kingdom.
Blind search would take too long; the Seeker instead used **rotated binary search**, always noticing which half of the land was still in sorted harmony.

Thus began the saga of **Search in Rotated Sorted Array**.

---

### 📜 The Seeker’s Compass and Map

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

A scroll of heights (`nums`) held the twisted kingdom’s arrangement.
Two markers, `l` and `r`, would divide the land repeatedly until the knight himself stood uncovered.

---

### 🧭 The Search Ritual — Rotated Binary Search

```cpp
int search(vector<int>& nums, int target) {
    int l = 0, r = nums.size() - 1;
```

The Seeker placed one foot at each end of the realm.

---

```cpp
    while (l <= r) {
        int mid = l + (r - l) / 2;
        if (nums[mid] == target) return mid;
```

With a single strike, the Seeker split the land at `mid`.
If the knight stood there, the quest ended immediately.

---

### ⚔️ The Law of the Straight Half

```cpp
        // left half is sorted
        if (nums[l] <= nums[mid]) {
```

The Seeker inspected the left half.
If the numbers flowed upward in true ascending order from `l` to `mid`, this side still obeyed the Old Law of Sortedness.

---

```cpp
            if (nums[l] <= target && target < nums[mid]) {
                r = mid - 1;  // knight lies in sorted left half
            } else {
                l = mid + 1;  // knight lies in twisted right half
            }
```

If the knight’s banner lay within the left half’s range, the Seeker rode left.
Otherwise, he journeyed right into the stranger, rotated lands.

---

### 🔄 The Twisted Half

```cpp
        } else {
            // right half is sorted
            if (nums[mid] < target && target <= nums[r]) {
                l = mid + 1;  // knight lies in sorted right half
            } else {
                r = mid - 1;  // knight must be in twisted left half
            }
        }
    }
    return -1; // knight not found
}
```

If the left half was not sorted, then by the kingdom’s nature, the **right half must be**.
Then the Seeker applied the same logic in reverse:
If the knight lay in the sorted right range, move right; else, left.

Step by step, land by land, one sorted half always guided the way.
If all paths failed, the knight was not in the kingdom at all.

---

### 🎺 The Trial of the Rotated Realm

```cpp
int main() {
    vector<int> nums = {4,5,6,7,0,1,2};
    int target = 0;
    cout << search(nums, target) << endl; // expected: 4
    return 0;
}
```

In the land `{4,5,6,7,0,1,2}`, the dawn had shifted to the middle.
The knight bearing banner `0` stood at position `4`.
The Seeker’s ritual found him swiftly, guided by the half that still followed the old order.

---

### 🧠 Memory of the Twisted Path

-   **Binary search with rotation logic** — always one half is sorted.
-   **Check if left half sorted:** `nums[l] <= nums[mid]`.
-   **Check if target in that sorted half:** move accordingly.
-   **Else** — the other half is sorted, repeat logic there.
-   **Time:** O(log n).
-   **Space:** O(1).

Thus is remembered the saga of **Search in Rotated Sorted Array**,
where the Seeker splits the twisted realm again and again,
following whichever half still honors the Old Sorted Law
until the lost knight’s banner is finally found. 🎯✨
