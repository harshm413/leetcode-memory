## ⚔️📜 _The Council of Four Fates: The 4Sum Saga_

> \*"In the Realm of Numbers,
> balance could no longer be achieved by two or three alone.
>
> A greater harmony was demanded —
> **four warriors**,
> standing together,
> whose combined strength equaled a sacred target **T**.
>
> The Oracle was commanded:
>
> \*\*‘Find all unique quartets
> whose sum equals the target.
>
> No repeated alliances.
> No duplicate destinies.’\*\*
>
> The Oracle knew brute force would shatter the realm.
>
> So she imposed **order**,
> fixed fate step by step,
> and let the remaining warriors
> seek balance through disciplined motion."\*

---

This is the saga of **4Sum**.

You are given:

-   an integer array `nums`
-   an integer `target`

Your task:

-   Find **all unique quadruplets** `[a, b, c, d]`
-   Such that `a + b + c + d = target`
-   No duplicate quadruplets allowed

---

## 🧠 The Oracle’s Core Insight — Fix Two, Balance Two

The Oracle extended the wisdom of **3Sum**:

1. **Sort** the array
2. Fix the **first warrior** `i`
3. Fix the **second warrior** `j`
4. Use **two pointers** to find the remaining pair
5. Skip duplicates at every step

Order reduces chaos.
Fixing fate reduces dimensions.

---

### 📜 The Scroll of Ordered Councils

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

---

## ⚔️ The Oracle’s Four-Warrior Ritual

```cpp
vector<vector<int>> fourSum(vector<int>& nums, int target) {
    vector<vector<int>> res;
    sort(nums.begin(), nums.end());
```

The realm was ordered.
Duplicates could now be silenced.

---

### 👑 Fix the First Warrior

```cpp
    int n = nums.size();
    for (int i = 0; i < n; i++) {
        if (i > 0 && nums[i] == nums[i - 1]) continue;
```

The Oracle chose the first fate
and skipped repeated leaders.

---

### 🏹 Fix the Second Warrior

```cpp
        for (int j = i + 1; j < n; j++) {
            if (j > i + 1 && nums[j] == nums[j - 1]) continue;
```

A second fate was fixed beside the first,
again skipping duplicate destinies.

---

### 🔍 Seek Balance with Two Pointers

```cpp
            int left = j + 1;
            int right = n - 1;
```

Two seekers searched the remaining realm.

---

### ⚖️ Measure the Council’s Strength

```cpp
            while (left < right) {
                long long sum =
                    (long long)nums[i] + nums[j] + nums[left] + nums[right];
```

The Oracle measured carefully —
large values demanded caution.

---

### 🏆 When Perfect Balance Is Found

```cpp
                if (sum == target) {
                    res.push_back({nums[i], nums[j], nums[left], nums[right]});
```

A perfect council was recorded.

---

```cpp
                    while (left < right && nums[left] == nums[left + 1]) left++;
                    while (left < right && nums[right] == nums[right - 1]) right--;
                    left++;
                    right--;
```

Duplicate alliances were skipped
before moving inward.

---

```cpp
                else if (sum < target) {
                    left++;
                }
                else {
                    right--;
                }
            }
        }
    }
    return res;
}
```

If the council was too weak or too strong,
the seekers adjusted accordingly.

---

### 🎺 The Trial of the Four Fates

```cpp
int main() {
    vector<int> nums = {1, 0, -1, 0, -2, 2};
    int target = 0;

    vector<vector<int>> ans = fourSum(nums, target);

    for (auto& quad : ans) {
        for (int x : quad) cout << x << " ";
        cout << endl;
    }
    return 0;
}
```

The Oracle revealed the councils:

-   `[-2, -1, 1, 2]`
-   `[-2,  0, 0, 2]`
-   `[-1,  0, 0, 1]`

Perfect balance.
No duplicates.

---

### 🧠 Memory of the Council Law

-   Sort the array first
-   Fix two elements, search for two
-   Use two pointers for inner balance
-   Skip duplicates at **all levels**
-   Use `long long` to avoid overflow
-   **Time:** O(n³)
-   **Space:** O(1) (excluding output)

Thus is remembered the saga of **4Sum**,
where the Oracle convenes a council of four fates,
fixing two destinies,
sending two seekers into the ordered realm —
and revealing every unique alliance
whose combined strength
meets the sacred target exactly. ⚔️✨
