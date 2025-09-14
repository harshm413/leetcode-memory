## 🕯️ *The Tale of the Mirror Shadows: Contains Duplicate Saga*

> *"Not every face in the crowd is unique.
> Some are echoes, shadows wearing another’s skin.
> To find them, you must look beyond the surface,
> into the Book of Seen Faces."*

---

Once upon a time, in a bustling market square, countless travelers gathered. Each carried a banner — a number marking their identity. The Oracle was tasked with ensuring harmony:

> *“If any traveler is but a shadow of another,
> we must know — for duplicates bring disorder.”*

Thus began the quest known as **Contains Duplicate**.

---

### 📜 The Scroll of Witnesses

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;
```

The scribes opened their scrolls (`iostream`) and gathered the line of travelers (`vector`). To aid in judgment, they summoned the **Book of Seen Faces** — the `unordered_set`.

This book had one power: it remembered every traveler it met. If ever a shadow tried to step in with the same banner, the book would cry out instantly.

---

### 🕵️ The Oracle’s Ritual

```cpp
bool containsDuplicate(vector<int>& nums) {
```

The Oracle began her ritual, invoking **containsDuplicate**. A wave spread across the market; all banners would now be inspected.

---

```cpp
    unordered_set<int> seen;
```

The **Book of Seen Faces** was laid open. Its pages were blank, eager to record the first banner that passed.

---

### 👣 The March of Travelers

```cpp
    for (int x : nums) {
```

One by one, travelers entered the square. Their banners (`x`) gleamed under the sun, each demanding recognition.

---

### 🪞 The Test of Shadows

```cpp
        if (seen.count(x)) {
            return true;
        }
```

The Oracle leaned over the book. If the banner `x` was already etched upon its pages, then this traveler was no unique soul, but a **shadow**, a duplicate.

The ritual would halt at once, declaring **true** — for the land had seen a repeat.

---

```cpp
        seen.insert(x);
```

But if the banner was new, the traveler was welcomed. His identity was written into the **Book of Seen Faces**, ensuring he would be recognized if ever a shadow tried to mimic him.

---

### 🌅 The End of the March

```cpp
    }
    return false;
}
```

When the last traveler had passed and no shadows were found, the Oracle closed the book. She declared **false** — no duplicates lurked that day.

Peace returned to the market square.

---

### 🎺 The Trial of Five

```cpp
int main() {
    vector<int> nums = {1, 2, 3, 4, 1};
```

One day, five travelers arrived with banners: 1, 2, 3, 4, 1. At first, all was well, until the final traveler entered.

The **Book of Seen Faces** cried out — *“I have seen banner 1 before!”*

A shadow had been revealed.

---

```cpp
    cout << (containsDuplicate(nums) ? "true" : "false") << endl;
}
```

The Oracle announced the judgment: *true*.

The people murmured, for a duplicate had indeed been found. The tale of the shadow spread across the land.

---

### 🧠 Memory of the Shadows

* The **unordered\_set** → the Book of Seen Faces.
* The **for loop** → the march of travelers.
* The **if (seen.count(x))** → the moment a shadow is caught.
* The **insert** → writing the banner into memory.
* **return true / false** → the Oracle’s final judgment.

Thus is remembered the saga of **Contains Duplicate**, where shadows reveal themselves only to those who carry the Book of Memory. 🕯️👥