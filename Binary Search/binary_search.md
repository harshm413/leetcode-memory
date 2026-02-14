## 🎯⚔️ _The Halving of the Infinite Line: The Binary Search Saga_

> \*"In the Realm of Ordered Numbers,
> values stood in perfect ascending discipline.
>
> The Oracle was commanded:
>
> **‘Find the target.’**
>
> But she was forbidden
> from walking the entire line step by step.
>
> Instead, she embraced a powerful truth:
>
> When the land is sorted,
> one glance at the middle
> can eliminate half the world.
>
> Thus began the ritual of division —
> halving again and again
> until truth stood alone."\*

---

This is the epic saga of **Binary Search**.

You are given:

-   a sorted array `nums`
-   a target value `target`

Your task:

-   Return the index of `target`
-   If not found → return `-1`

---

## 🧠 The Oracle’s Core Insight — Divide and Conquer

At each step:

-   Find the middle index.
-   If `nums[mid] == target` → found.
-   If `nums[mid] < target` → search right half.
-   If `nums[mid] > target` → search left half.

Each step eliminates half the search space.

---

## ⚔️ The Oracle’s Halving Ritual

```cpp
int binarySearch(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
```

Two sentinels guard the boundaries.

---

### 🌊 Continue While Search Space Exists

```cpp
    while (left <= right) {
        int mid = left + (right - left) / 2;
```

The Oracle avoids overflow
by computing mid carefully.

---

### 🎯 Compare with Target

```cpp
        if (nums[mid] == target) {
            return mid;
        }
```

If equal, the quest ends.

---

### 🔻 Search Right Half

```cpp
        else if (nums[mid] < target) {
            left = mid + 1;
        }
```

Target lies beyond the middle.

---

### 🔺 Search Left Half

```cpp
        else {
            right = mid - 1;
        }
    }
```

Target lies before the middle.

---

### ❌ If Not Found

```cpp
    return -1;
}
```

If the search space vanishes,
the target does not exist.

---

### 🎺 The Trial of Division

```cpp
int main() {
    vector<int> nums = {-1,0,3,5,9,12};
    int target = 9;

    cout << binarySearch(nums, target) << endl;
    // expected: 4
    return 0;
}
```

The Oracle halved the world
again and again
until only `9` remained.

---

## 🧠 Memory of the Halving Law

-   Works only on sorted arrays
-   Compare with middle element
-   Eliminate half each step
-   Loop until `left <= right`
-   **Time:** O(log n)
-   **Space:** O(1)

---

Thus is remembered the saga of
**Binary Search**,
where the Oracle conquers vast ordered lands
not by walking step by step —
but by slicing the world in halves,
until the target stands revealed
in the narrowing light of precision. 🎯✨
