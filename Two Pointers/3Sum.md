## ⚔️📜 _The Triad of Perfect Balance: The 3Sum Saga_

> \*"In the Realm of Numbers,
> three warriors were needed —
> not two, not four —
> exactly **three** whose combined strength
> would balance the world to **zero**.
>
> The Oracle was commanded:
>
> \*\*‘Find all unique triplets
> whose sum is zero.
>
> No duplicate alliances.
> No repeated destinies.’\*\*
>
> Chaos lurked in repetition,
> but order could tame it.
>
> So the Oracle first **sorted the realm**,
> then fixed one warrior at a time,
> letting the other two search for harmony."\*

---

This is the saga of **3Sum**.

You are given an integer array `nums`.
Your task:

-   Find **all unique triplets** `[a, b, c]`
-   Such that `a + b + c = 0`
-   The solution set must not contain duplicates

---

## 🧠 The Oracle’s Core Insight — Fix One, Balance Two

The Oracle followed a powerful strategy:

1. **Sort** the array
2. Fix one element `i`
3. Use **two pointers** to find pairs that sum to `-nums[i]`
4. Skip duplicates to avoid repeated triplets

Sorting transformed chaos into direction.

---

### 📜 The Scroll of Ordered Destiny

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## ⚔️ The Oracle’s Three-Warrior Ritual

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>> res;
    sort(nums.begin(), nums.end());
```

The realm was ordered.
Duplicate destinies could now be avoided.

---

### 🧭 Fix the First Warrior

```cpp
    for (int i = 0; i < nums.size(); i++) {
        if (i > 0 && nums[i] == nums[i - 1]) continue;
```

The Oracle fixed one warrior
and skipped identical leaders to prevent repetition.

---

### 🔍 Seek Balance with Two Pointers

```cpp
        int left = i + 1;
        int right = nums.size() - 1;
```

Two seekers searched the remaining realm.

---

### ⚖️ Walk Until Balance Is Found

```cpp
        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];
```

The strength of the triad was measured.

---

### 🏹 Adjust the Seekers

```cpp
            if (sum == 0) {
                res.push_back({nums[i], nums[left], nums[right]});
```

A perfect triad was found and recorded.

---

```cpp
                while (left < right && nums[left] == nums[left + 1]) left++;
                while (left < right && nums[right] == nums[right - 1]) right--;
                left++;
                right--;
```

Duplicates were skipped,
and the seekers moved inward.

---

```cpp
            else if (sum < 0) {
                left++;
            }
            else {
                right--;
            }
        }
    }
    return res;
}
```

If the sum was too small or too large,
the seekers adjusted accordingly.

---

### 🎺 The Trial of the Balanced Triads

```cpp
int main() {
    vector<int> nums = {-1, 0, 1, 2, -1, -4};
    vector<vector<int>> ans = threeSum(nums);

    for (auto& triplet : ans) {
        for (int x : triplet) cout << x << " ";
        cout << endl;
    }
    return 0;
}
```

The Oracle revealed the balanced triads:

-   `[-1, -1, 2]`
-   `[-1, 0, 1]`

No duplicates.
Perfect balance.

---

### 🧠 Memory of the Triad Law

-   Sort the array first
-   Fix one element at a time
-   Use two pointers to find remaining pair
-   Skip duplicates at all levels
-   Ensure unique triplets only
-   **Time:** O(n²)
-   **Space:** O(1) (excluding output)

Thus is remembered the saga of **3Sum**,
where the Oracle transforms chaos into order,
fixes one destiny,
and sends two seekers to find balance —
revealing every unique triad
whose combined strength
restores harmony to zero. ⚔️✨
