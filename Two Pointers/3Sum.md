# 🔺 _The Three-Heart Pact: The 3Sum Saga_

> _"When three wanderers meet, their fortunes may cancel —
> one’s burden balanced by the other's gift and the third’s secret.
> Seek those triads whose sum is zero, and bind them as a covenant."_

---

The market was full of numbers — some heavy with debt, some light with coin, some balanced in spirit. The Council sought triads of travelers that, when they pooled their burdens, yielded perfect balance: a sum of **zero**.
The Keeper would not simply try every trio; instead she would first lay the numbers in order, then let two scouts sweep from both sides to find partners for each chosen leader, while carefully avoiding repeated covenants.

Thus began the quest of **3Sum**.

---

### 📜 The Gathered Scrolls

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
```

The scribes brought order (`sort`) to the line and readied a ledger (`vector<vector<int>>`) to record unique triplets.

---

### 🔍 The Keeper’s Rite

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>> res;
    sort(nums.begin(), nums.end());
    int n = nums.size();
```

The Keeper laid out the numbers in ascending order. With the line sorted, duplicates cluster and two scouts can walk inward to find perfect partners faster than wandering randomly.

---

### 🧭 Choose a Leader, Send Two Scouts

```cpp
    for (int i = 0; i < n; ++i) {
        if (i > 0 && nums[i] == nums[i-1]) continue; // skip same leader
        int target = -nums[i];
        int l = i + 1, r = n - 1;
```

For each position `i`, the Keeper picks a leader `nums[i]`. If this leader is the same as the previous one, she skips him — to avoid recording the same covenant twice. The scouts `l` and `r` start immediately to the right and at the end, seeking two numbers that sum to `target = -nums[i]`.

---

### ⚔️ The Two-Scout Sweep

```cpp
        while (l < r) {
            int sum = nums[l] + nums[r];
            if (sum == target) {
                res.push_back({nums[i], nums[l], nums[r]});
                // advance both scouts and skip duplicates
                while (l < r && nums[l] == nums[l+1]) ++l;
                while (l < r && nums[r] == nums[r-1]) --r;
                ++l; --r;
            } else if (sum < target) {
                ++l; // need bigger sum: move left scout right
            } else {
                --r; // need smaller sum: move right scout left
            }
        }
    }
    return res;
}
```

If the scouts find numbers whose sum equals the leader’s `target`, a covenant `{nums[i], nums[l], nums[r]}` is recorded. Then both scouts advance — but they skip over any duplicates so the same trio is never logged twice. If the pair is too small, the left scout advances to grow the sum; if too large, the right scout retreats to shrink it.

---

### 🎺 The Trial of Many Numbers

```cpp
int main() {
    vector<int> nums = {-1, 0, 1, 2, -1, -4};
    vector<vector<int>> ans = threeSum(nums);
    for (auto &trip : ans) {
        cout << "[";
        for (int i = 0; i < trip.size(); ++i) {
            if (i) cout << ", ";
            cout << trip[i];
        }
        cout << "]\n";
    }
    return 0;
}
```

From the crowd `{-1, 0, 1, 2, -1, -4}` the Keeper finds the covenants that balance to zero. The recorded triads, unique and ordered by the Keeper’s sorting, reveal which three hearts together form a perfect pact.

---

### 🧠 Memory of the Pact

-   **Sort first** — makes duplicates adjacent and enables two-pointer scanning.
-   **Skip duplicate leaders (`i`)** — avoid repeating the same triplet.
-   **Two pointers `l` and `r`** — find pairs summing to `-nums[i]` in linear time per leader.
-   **Skip duplicate `l` / `r` values after a hit** — ensure uniqueness of recorded triplets.
-   **Overall complexity:** O(n²) time, O(log n) to O(n) space depending on sort implementation and output size.

Thus is remembered the saga of **3Sum**: choose a leader, send two scouts, and bind every unique trio whose burdens cancel to zero into an everlasting pact.
