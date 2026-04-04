## 🎯👬 _The Hunt for the Perfect Pair: The Two Sum Saga_

> \_"In the Kingdom of Numbers,
> warriors stood in a long line --
> each carrying a banner marked with their strength.
>
> The Oracle was summoned with a sacred decree:
>
> **'Find two warriors whose strengths
> add up to exactly the sacred target.
> Return their positions in the line.'**
>
> The naive approach was brute force --
> compare every warrior with every other warrior.
> O(N^2) -- slow, wasteful, unworthy of the Oracle.
>
> The Oracle knew a better way.
>
> She opened the **Book of Indices** --
> a hash map that remembered every warrior she had already passed.
>
> As she walked the line, at each warrior she asked:
>
> **'I need a partner whose strength is (target - mine).
> Has such a partner already passed through?'**
>
> If YES -- the Book revealed the partner's index.
> The pair was found. The hunt was over.
>
> If NO -- the current warrior was inscribed into the Book,
> so future warriors could find them as a partner.
>
> One pass. One map. The perfect pair revealed."\_

---

This is the saga of **Two Sum**.

You are given an integer array `nums` and an integer `target`.

Your task:

-   Return the **indices** of the two numbers that add up to `target`.
-   Each input has **exactly one solution**.
-   You may not use the same element twice.

```
Input:  nums = [2, 7, 11, 15], target = 9
Output: [0, 1]   (nums[0] + nums[1] = 2 + 7 = 9)

Input:  nums = [3, 2, 4], target = 6
Output: [1, 2]   (nums[1] + nums[2] = 2 + 4 = 6)

Input:  nums = [3, 3], target = 6
Output: [0, 1]   (nums[0] + nums[1] = 3 + 3 = 6)
```

---

## 🧠 The Oracle's Core Insight -- Complement Lookup

For each element `nums[i]`, the Oracle computes:

```
need = target - nums[i]
```

This is the **complement** -- the exact value a partner must carry
for the pair to sum to `target`.

If `need` already exists in the hash map --
the partner was seen earlier. Return both indices.

If not -- record `nums[i]` and its index in the map
so future elements can find it as their partner.

The hash map turns an O(N) search for the partner
into an O(1) lookup.

```
Time:  O(N) -- single pass, O(1) lookups
Space: O(N) -- the hash map stores at most N entries
```

---

### 📜 The Scroll of the Hunt

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

---

## 🔮 The Oracle's One-Pass Ritual

```cpp
vector<int> twoSum(vector<int>& nums, int target) {
    unordered_map<int, int> seen;
```

The Oracle opened the **Book of Indices** --
a map from each warrior's value to their index in the line.

It began empty -- no warriors had been encountered yet.

---

## 👣 Walk the Line -- One Warrior at a Time

```cpp
    for (int i = 0; i < nums.size(); i++) {
        int need = target - nums[i];
```

At each position `i`, the Oracle computed the complement --
the exact value needed to complete the pair.

If `target = 9` and `nums[i] = 2`, then `need = 7`.
The Oracle needed a warrior carrying `7`.

---

### 🔍 Has the Partner Already Passed?

```cpp
        if (seen.count(need)) {
            return {seen[need], i};
        }
```

The Oracle checked the Book:

> **"Has a warrior with value `need` already been inscribed?"**

If YES -- `seen[need]` gave the partner's index.
The pair `{seen[need], i}` was returned immediately.
The hunt was over.

If NO -- the partner had not yet appeared.
The search continued.

> _"The Oracle never looks ahead.
> She only remembers the past.
> And the past is enough --
> because when the second half of a pair arrives,
> the first half is already in the Book."_

---

### ✍️ Inscribe the Current Warrior

```cpp
        seen[nums[i]] = i;
    }
```

If no partner was found for this warrior,
they were inscribed into the Book --
their value mapped to their index.

Future warriors would check the Book
and might find this warrior as their missing half.

---

## 🏁 No Pair Found (Safety Return)

```cpp
    return {};
}
```

If the loop ended without finding a pair,
an empty result was returned.

The problem guarantees exactly one solution exists,
so this line is a safety net that should never be reached.

---

### 🎺 The Trial of the Perfect Pair

```cpp
int main() {
    vector<int> nums1 = {2, 7, 11, 15};
    auto ans1 = twoSum(nums1, 9);
    cout << ans1[0] << " " << ans1[1] << endl; // expected: 0 1

    vector<int> nums2 = {3, 2, 4};
    auto ans2 = twoSum(nums2, 6);
    cout << ans2[0] << " " << ans2[1] << endl; // expected: 1 2

    vector<int> nums3 = {3, 3};
    auto ans3 = twoSum(nums3, 6);
    cout << ans3[0] << " " << ans3[1] << endl; // expected: 0 1

    return 0;
}
```

---

**Full trace for `[2, 7, 11, 15]`, target = 9:**

| i | nums[i] | need = 9 - nums[i] | need in seen? | Action              | seen after     |
| - | ------- | ------------------- | ------------- | ------------------- | -------------- |
| 0 | 2       | 7                   | No            | Inscribe {2: 0}     | {2:0}          |
| 1 | 7       | 2                   | Yes (at 0)    | Return **{0, 1}**   | --             |

**Answer: [0, 1]** ✓

At index 1, the Oracle needed `2`. The Book showed `2` was at index 0.
`nums[0] + nums[1] = 2 + 7 = 9`.

---

**Full trace for `[3, 2, 4]`, target = 6:**

| i | nums[i] | need = 6 - nums[i] | need in seen? | Action              | seen after     |
| - | ------- | ------------------- | ------------- | ------------------- | -------------- |
| 0 | 3       | 3                   | No            | Inscribe {3: 0}     | {3:0}          |
| 1 | 2       | 4                   | No            | Inscribe {2: 1}     | {3:0, 2:1}    |
| 2 | 4       | 2                   | Yes (at 1)    | Return **{1, 2}**   | --             |

**Answer: [1, 2]** ✓

At index 2, the Oracle needed `2`. The Book showed `2` was at index 1.
`nums[1] + nums[2] = 2 + 4 = 6`.

---

**Full trace for `[3, 3]`, target = 6:**

| i | nums[i] | need = 6 - nums[i] | need in seen? | Action              | seen after     |
| - | ------- | ------------------- | ------------- | ------------------- | -------------- |
| 0 | 3       | 3                   | No            | Inscribe {3: 0}     | {3:0}          |
| 1 | 3       | 3                   | Yes (at 0)    | Return **{0, 1}**   | --             |

**Answer: [0, 1]** ✓

Both warriors carried `3`. At index 1, the Oracle needed `3`.
The Book showed `3` was at index 0. The pair was found.

This trace proves the algorithm handles **duplicate values** correctly --
because we check the Book BEFORE inscribing the current element,
so a number never matches with itself.

---

## 🔍 Why Check Before Inscribing?

The order matters:

```
1. Compute need
2. Check if need is in the map    <-- FIRST
3. Inscribe current element       <-- SECOND
```

If we inscribed first and then checked,
a number could find itself as its own partner.

For `[3, 3]` with target `6`:
-   If we inscribed `3` at index 0 first, then checked `need = 3` --
    we'd find index 0 and return `{0, 0}` -- WRONG.
-   By checking first, index 0's `3` is not yet in the map,
    so it gets inscribed. At index 1, `need = 3` finds index 0 -- correct.

---

### 🧠 Memory of the Perfect Pair Law

-   **Hash map `seen`:** maps each value to its index -- O(1) lookup
-   **Complement:** `need = target - nums[i]` -- the missing half of the pair
-   **Check before inscribe:** prevents a number from pairing with itself
-   **One pass:** each element is either the answer or becomes a candidate for future elements
-   **Exactly one solution** guaranteed -- the loop always finds it before ending
-   **Time:** O(N) -- single pass with O(1) hash lookups
-   **Space:** O(N) -- the map stores at most N entries
-   **Edge cases:**
    -   Duplicate values `[3, 3]` -- handled correctly by check-before-inscribe order
    -   Negative numbers -- complement arithmetic works identically
    -   Two elements -- the simplest case, always returns `{0, 1}`

Thus is remembered the saga of **Two Sum**,
where the Oracle walked a line of warriors
carrying the Book of Indices --
computing each warrior's missing half,
checking if that half had already passed,
and inscribing each warrior for the benefit of those yet to come --
until the destined pair was found
in a single, elegant pass. 🎯👬✨
