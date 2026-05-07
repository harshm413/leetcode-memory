## 🔤∩ _The Shared Letters: The Find Common Characters Saga_

> \_"The Oracle was given a list of strings.
>
> She was commanded:
>
> **'Find all characters that appear in EVERY string,
> including duplicates.'**
>
> If 'a' appears twice in every string → include 'a' twice.
> If 'a' appears once in one string but twice in another →
> include 'a' only once (the minimum across all strings).
>
> The Oracle's approach:
>
> **Frequency intersection.**
>
> Start with the frequency of the first string.
> For each subsequent string, take the MINIMUM frequency
> of each character across both.
> After processing all strings, the remaining frequencies
> are the common characters."\_

---

This is the saga of **Find Common Characters (LeetCode 1002)**.

Given an array of strings `words`:
-   Return a list of all characters that appear in every string,
    including duplicates.

```
Input:  words = ["bella", "label", "roller"]
Output: ["e", "l", "l"]

Input:  words = ["cool", "lock", "cook"]
Output: ["c", "o"]
```

---

## 🧠 The Frequency Intersection Insight

For each character, the number of times it appears in the result
= the MINIMUM frequency across ALL strings.

```
"bella": a=1, b=1, e=1, l=2
"label": a=1, b=1, e=1, l=2
"roller": e=1, l=2, o=1, r=2

Min across all:
  a: min(1, 1, 0) = 0  → not common
  b: min(1, 1, 0) = 0  → not common
  e: min(1, 1, 1) = 1  → include 'e' once
  l: min(2, 2, 2) = 2  → include 'l' twice
```

Result: ["e", "l", "l"] ✓

---

## 🧠 The Algorithm

1. Initialize `minFreq[26]` with the frequencies of the first word.
2. For each subsequent word, compute its frequency array.
   Take `min(minFreq[c], wordFreq[c])` for each character.
3. After all words, `minFreq[c]` = how many times character `c`
   appears in the result.
4. Build the result from `minFreq`.

---

### 📜 The Scroll of the Shared Letters

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
```

---

## ∩ The Frequency Intersection Solution

### Initialize with the first word's frequencies

```cpp
vector<string> commonChars(vector<string>& words) {
    vector<int> minFreq(26, 0);
    for (char c : words[0]) {
        minFreq[c - 'a']++;
    }
```

Start with the first word's character counts.
This is the "maximum possible" — subsequent words can only reduce it.

---

### Intersect with each subsequent word

```cpp
    for (int i = 1; i < words.size(); i++) {
        vector<int> wordFreq(26, 0);
        for (char c : words[i]) {
            wordFreq[c - 'a']++;
        }
```

Compute the frequency array for the current word.

---

### Take the minimum for each character

```cpp
        for (int c = 0; c < 26; c++) {
            minFreq[c] = min(minFreq[c], wordFreq[c]);
        }
    }
```

For each character, keep only the minimum count seen so far.
If a character appears 3 times in word 1 but only 1 time in word 2,
it can only appear 1 time in the result.

> _"Each new word can only REDUCE the common count.
> A character's count in the result = the bottleneck —
> the word where it appears the fewest times."_

---

### Build the result

```cpp
    vector<string> result;
    for (int c = 0; c < 26; c++) {
        for (int j = 0; j < minFreq[c]; j++) {
            result.push_back(string(1, 'a' + c));
        }
    }
    return result;
}
```

For each character with `minFreq[c] > 0`, add it `minFreq[c]` times.

---

### 🎺 The Trial of the Shared Letters

```cpp
int main() {
    vector<string> w1 = {"bella", "label", "roller"};
    auto r1 = commonChars(w1);
    for (auto& s : r1) cout << s << " ";
    cout << endl; // expected: e l l

    vector<string> w2 = {"cool", "lock", "cook"};
    auto r2 = commonChars(w2);
    for (auto& s : r2) cout << s << " ";
    cout << endl; // expected: c o

    return 0;
}
```

---

**Trace for words = ["bella", "label", "roller"]:**

```
After "bella":  minFreq = {a:1, b:1, e:1, l:2}

After "label":  wordFreq = {a:1, b:1, e:1, l:2}
  min: a=min(1,1)=1, b=min(1,1)=1, e=min(1,1)=1, l=min(2,2)=2
  minFreq = {a:1, b:1, e:1, l:2}

After "roller": wordFreq = {e:1, l:2, o:1, r:2}
  min: a=min(1,0)=0, b=min(1,0)=0, e=min(1,1)=1, l=min(2,2)=2, o=min(0,1)=0, r=min(0,2)=0
  minFreq = {e:1, l:2}

Result: ["e", "l", "l"]
```

**Answer: ["e", "l", "l"]** ✓

---

**Trace for words = ["cool", "lock", "cook"]:**

```
After "cool":  minFreq = {c:1, l:1, o:2}
After "lock":  wordFreq = {c:1, k:1, l:1, o:1}
  min: c=1, l=1, o=min(2,1)=1
  minFreq = {c:1, l:1, o:1}
After "cook":  wordFreq = {c:1, k:1, o:2}
  min: c=1, l=min(1,0)=0, o=min(1,2)=1
  minFreq = {c:1, o:1}

Result: ["c", "o"]
```

**Answer: ["c", "o"]** ✓

---

## 🔍 Why Minimum and Not Something Else?

**Minimum** because a character can only be "common" if it appears
in EVERY string. The string with the fewest occurrences is the bottleneck.

If 'l' appears 5 times in one string but 2 times in another,
you can only guarantee 2 common 'l's — the minimum.

This is the same logic as set intersection with multiplicity.

---

## 🔍 Time and Space Complexity

**Time:** O(N × L) where N = number of words, L = average word length.
Each word is scanned once to build its frequency array.
The 26-character min comparison is O(1) per word.

**Space:** O(1) — two arrays of size 26. Constant.

---

### 🧠 Memory of the Shared Letters Law

-   **Frequency intersection:** `minFreq[c] = min across all words`
-   **Initialize** with first word's frequencies
-   **For each word:** compute wordFreq, take min with minFreq
-   **Build result:** add each character `minFreq[c]` times
-   **Minimum = bottleneck** — the word with fewest occurrences limits the count
-   **Time:** O(N × L). **Space:** O(1).

Thus is remembered the saga of **Find Common Characters**,
where the Oracle intersected frequencies across all words —
each new word could only reduce the common count,
the minimum frequency being the bottleneck —
until only the characters shared by ALL words remained,
each appearing exactly as many times
as the most restrictive word allowed. 🔤∩✨
