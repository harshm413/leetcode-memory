## 🎯 _The Hunt for the Perfect Pair: The Two Sum Saga_

> _"Among many numbers wandering the land,
> two alone are bound by destiny.
> Together they form the sacred sum,
> and only the Oracle can reveal their bond."_

---

In the kingdom of numbers, the people cried for prophecy. They sought not all, but **two chosen warriors** whose strengths together equaled the sacred target.

The Oracle was summoned to discover this fated pair. Thus began the saga of the **Two Sum**.

---

### 📜 The Scroll of the Hunt

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;
```

The scribes brought forth the tools: the **line of warriors** (`vector`) and the **Book of Indices** (`unordered_map`), which could recall any warrior’s banner and where he stood.

---

### 🎯 The Oracle’s Ritual

```cpp
vector<int> twoSum(vector<int>& nums, int target) {
```

The Oracle raised her hand and began the spell **twoSum**. The line of warriors `nums` stood ready, while the sacred number `target` burned in the air above them.

---

### 📖 The Book of Indices

```cpp
    unordered_map<int, int> seen;
```

The **Book of Indices** was opened. It would remember each warrior’s banner (value) and his place in line (index).

---

### 👣 The March of Warriors

```cpp
    for (int i = 0; i < nums.size(); i++) {
        int need = target - nums[i];
```

One by one, each warrior stepped forward. The Oracle looked upon him and whispered:

> _“If you are to complete the prophecy,
> then I must find one who carries the banner `need`.”_

---

### 🔍 The Check of Destiny

```cpp
        if (seen.count(need)) {
            return {seen[need], i};
        }
```

The Oracle searched the Book. If the warrior `need` had already passed, then destiny was fulfilled.

The Oracle cried out the indices: `{seen[need], i}` — the two chosen warriors who together formed the sacred sum.

---

### ✍️ Writing in the Book

```cpp
        seen[nums[i]] = i;
    }
    return {};
}
```

But if the partner was not yet found, the Oracle inscribed the current warrior into the Book, marking his banner and place.

If no pair was discovered by the end, the ritual returned an empty result — the prophecy had no answer that day.

---

### 🎺 The Trial of Four

```cpp
int main() {
    vector<int> nums = {2, 7, 11, 15};
    int target = 9;
```

Four warriors stood: 2, 7, 11, 15. The sacred target was 9.

The Oracle began her hunt.

---

```cpp
    vector<int> ans = twoSum(nums, target);
    for (int x : ans) cout << x << " ";
    cout << endl;
}
```

The Oracle revealed: `{0, 1}`. The first warrior (2) and the second (7) together fulfilled the prophecy.

The crowd bowed, for the pair had been found.

---

### 🧠 Memory of the Pair

-   **unordered_map** → the Book of Indices, remembering past warriors.
-   **need = target - nums\[i]** → the missing half of destiny.
-   **if (seen.count(need))** → check if the partner has already appeared.
-   **return {seen\[need], i}** → the revelation of the chosen pair.
-   **seen\[nums\[i]] = i** → inscribe each warrior into the Book for future checks.

Thus is remembered the saga of the **Two Sum**, where two destined warriors together completed the sacred target. 🎯👬
