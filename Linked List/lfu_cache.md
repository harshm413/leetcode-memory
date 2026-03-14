## 🏛️📊 _The Chamber of Counted Memories: The LFU Cache Saga_

> \_"The Kingdom had once built the Chamber of Recent Memories —
> the LRU Cache — where the least recently visited memory was evicted.
>
> But the new King was wiser and colder:
>
> **'Recent visits alone do not determine worth.
> A memory visited a thousand times yesterday
> is more valuable than one visited once today.
> I do not care who was visited last.
> I care who was visited the LEAST.'**
>
> This was the Law of the LFU —
> **Least Frequently Used must leave first.**
>
> The Keeper of the new Chamber thought carefully.
>
> She needed to answer three questions in O(1) time:
>
> **One:** When a key is accessed — find it instantly and increment its frequency.
> **Two:** When eviction is needed — find the key with the LOWEST frequency instantly.
> **Three:** Among all keys with the same lowest frequency — evict the LEAST RECENTLY USED one.
>
> Three data structures answered all three questions:
>
> A **Key-Value Map** — to find any key's value instantly.
> A **Key-Frequency Map** — to find any key's current frequency instantly.
> A **Frequency-to-OrderedList Map** — for each frequency,
> a list of keys at that frequency, ordered by recency of use.
>
> And one integer — **minFreq** —
> always tracking the current lowest frequency in the Chamber.
>
> Every get, every put — O(1).
> The most forgotten memory always fell first."\_

---

This is the saga of **LFU Cache**.

Design a data structure following the
**Least Frequently Used (LFU)** cache policy.

Implement the `LFUCache` class:

-   `LFUCache(int capacity)` — initialize with fixed capacity.
-   `int get(int key)` — return the value if key exists, else `-1`.
    **Increment the key's frequency.**
-   `void put(int key, int value)` — insert or update the key.
    **Increment the key's frequency.**
    If capacity is exceeded — **evict the least frequently used key.**
    If there is a tie in frequency — **evict the least recently used among them.**

All operations must run in **O(1)** time.

---

## 🧠 The Oracle's Core Insight — Three Maps and One MinFreq Sentinel

To answer all queries in O(1), the Chamber maintained:

```
keyVal   : key  → value          (find value instantly)
keyFreq  : key  → frequency      (find/update frequency instantly)
freqKeys : freq → list of keys   (ordered by recency: front=most recent,
                                  back=least recent — for same-freq eviction)
minFreq  : the current minimum frequency in the Chamber
```

**The secret weapon** is `freqKeys` — a map from each frequency
to a **doubly-ended ordered list** of keys at that frequency.

When a key's frequency is incremented from `f` to `f+1`:

-   Remove the key from `freqKeys[f]`
-   Add it to the **front** of `freqKeys[f+1]` (most recently used at new freq)
-   If `freqKeys[f]` becomes empty AND `f == minFreq` → increment `minFreq`

When eviction is needed:

-   The victim is at the **back** of `freqKeys[minFreq]`
    (least recently used among all keys with the lowest frequency)

This is the entire design. Three maps. One sentinel. O(1) everywhere.

---

### 📜 The Scroll of the Counted Chamber

```cpp
#include <iostream>
#include <unordered_map>
#include <list>
using namespace std;
```

`list` in C++ is a doubly linked list with O(1) front/back insert and erase.
It is the ordered keeper of keys at each frequency level.

---

## 🏛️ The Chamber is Constructed

```cpp
class LFUCache {
    int cap, minFreq;
    unordered_map<int, int> keyVal;
    unordered_map<int, int> keyFreq;
    unordered_map<int, list<int>> freqKeys;
    unordered_map<int, list<int>::iterator> keyIter;
```

Four maps and one sentinel:

-   `cap` — maximum capacity of the Chamber.
-   `minFreq` — the current minimum frequency. The eviction pointer.
-   `keyVal[key]` — the stored value for this key.
-   `keyFreq[key]` — the current frequency count for this key.
-   `freqKeys[f]` — the list of all keys currently at frequency `f`,
    ordered front-to-back as most-recently-used to least-recently-used.
-   `keyIter[key]` — an **iterator** pointing to where this key sits
    inside `freqKeys[keyFreq[key]]`. This is what enables O(1) removal
    from the middle of the list without searching.

> _"`keyIter` is the Keeper's finger —
> always marking exactly where each key sits in its frequency list.
> Without it, removing a key from its list would take O(n)."_

---

```cpp
public:
    LFUCache(int capacity) : cap(capacity), minFreq(0) {}
```

The Chamber opened — empty, with `minFreq` at zero.

---

## 🔧 The Private Ritual — Increment Frequency

_Every get and put uses this. It is the heart of the LFU._

```cpp
    void incrementFreq(int key) {
        int f = keyFreq[key];
        keyFreq[key]++;
        freqKeys[f].erase(keyIter[key]);
```

The key's current frequency `f` was read.
Its frequency was bumped to `f+1`.
It was **erased** from `freqKeys[f]` using its stored iterator —
a direct O(1) removal, no searching.

---

```cpp
        if (freqKeys[f].empty() && f == minFreq) {
            minFreq++;
        }
```

If erasing this key emptied the list at frequency `f`,
AND `f` was the current minimum frequency —
then no key at frequency `f` remained in the Chamber.

The minimum frequency must rise.
`minFreq` was incremented.

This is the **only place `minFreq` ever increases** —
and it always increases by exactly 1,
because the key just moved from `f` to `f+1`.

---

```cpp
        freqKeys[f + 1].push_front(key);
        keyIter[key] = freqKeys[f + 1].begin();
    }
```

The key was added to the **front** of `freqKeys[f+1]` —
the most recently used position at its new frequency.

Its iterator in `keyIter` was updated to point to this new location.

The key had been promoted. Its frequency rose. Its position updated.
Everything remained consistent — in O(1).

---

## 🔍 The GET Operation — Retrieve and Honor

```cpp
    int get(int key) {
        if (keyVal.find(key) == keyVal.end()) return -1;
        incrementFreq(key);
        return keyVal[key];
    }
```

The Keeper was asked for a key.

If it did not exist in `keyVal` — return `-1`.

If it did — increment its frequency (promoting it in its frequency list),
then return its value.

Simple. One check. One frequency promotion. One return.

---

## 📝 The PUT Operation — Store and Possibly Evict

```cpp
    void put(int key, int value) {
        if (cap <= 0) return;
```

A zero-capacity Chamber can store nothing. Guard immediately.

---

```cpp
        if (keyVal.find(key) != keyVal.end()) {
            keyVal[key] = value;
            incrementFreq(key);
            return;
        }
```

If the key **already existed** —
update its value and increment its frequency.
No eviction needed — the count of keys did not change.
Return immediately.

---

```cpp
        if ((int)keyVal.size() == cap) {
            int lfu = freqKeys[minFreq].back();
            freqKeys[minFreq].pop_back();
            keyIter.erase(lfu);
            keyFreq.erase(lfu);
            keyVal.erase(lfu);
        }
```

The Chamber was at full capacity.
Eviction was required.

The victim was found instantly:
`freqKeys[minFreq].back()` —
the key at the **back** of the list at the minimum frequency —
the least recently used key among the least frequently used keys.

It was removed from all four maps.
The Chamber made room.

---

```cpp
        keyVal[key]   = value;
        keyFreq[key]  = 1;
        freqKeys[1].push_front(key);
        keyIter[key]  = freqKeys[1].begin();
        minFreq       = 1;
    }
};
```

The new key entered the Chamber at **frequency 1** —
freshly seen for the first time.

It was added to the front of `freqKeys[1]`,
its iterator was recorded,
and `minFreq` was reset to `1` —
because this brand new key is now the least frequently used.

> _"Every new memory arrives with frequency 1.
> And frequency 1 is always the new minimum
> when a fresh key enters the Chamber."_

---

### 🎺 The Trial of the Counted Chamber

```cpp
int main() {
    LFUCache cache(2);

    cache.put(1, 1);   // freq: {1:1}         freqKeys: {1:[1]}        minFreq=1
    cache.put(2, 2);   // freq: {1:1, 2:1}    freqKeys: {1:[2,1]}      minFreq=1
    cout << cache.get(1) << endl; // 1 → freq: {1:2, 2:1} freqKeys:{1:[2],2:[1]} minFreq=1
    cache.put(3, 3);   // evict key 2 (minFreq=1, back of freqKeys[1])
                       // freq: {1:2, 3:1}    freqKeys:{1:[3],2:[1]}   minFreq=1
    cout << cache.get(2) << endl; // -1 (evicted)
    cout << cache.get(3) << endl; // 3 → freq: {1:2, 3:2} freqKeys:{2:[3,1]}    minFreq=2
    cache.put(4, 4);   // evict key 1 (minFreq=2, back of freqKeys[2] = key 1)
                       // freq: {3:2, 4:1}    freqKeys:{1:[4],2:[3]}   minFreq=1
    cout << cache.get(1) << endl; // -1 (evicted)
    cout << cache.get(3) << endl; // 3 → freq: {3:3, 4:1} freqKeys:{1:[4],3:[3]} minFreq=1
    cout << cache.get(4) << endl; // 4 → freq: {3:3, 4:2} freqKeys:{2:[4],3:[3]} minFreq=2
    return 0;
}
```

| Operation | Action                                          | Result |
| --------- | ----------------------------------------------- | ------ |
| put(1,1)  | freq[1]=1, freqKeys[1]=[1], min=1               | —      |
| put(2,2)  | freq[2]=1, freqKeys[1]=[2,1], min=1             | —      |
| get(1)    | freq[1]→2, freqKeys:{1:[2],2:[1]}               | **1**  |
| put(3,3)  | evict 2 (back of freqKeys[1]), freq[3]=1, min=1 | —      |
| get(2)    | not found                                       | **-1** |
| get(3)    | freq[3]→2, freqKeys[2]=[3,1], min=2             | **3**  |
| put(4,4)  | evict 1 (back of freqKeys[2]), freq[4]=1, min=1 | —      |
| get(1)    | not found                                       | **-1** |
| get(3)    | freq[3]→3, freqKeys[3]=[3]                      | **3**  |
| get(4)    | freq[4]→2, freqKeys[2]=[4], min=2               | **4**  |

The Chamber never overflowed.
The least frequently visited memory always fell first.
Among ties — the least recently used among them fell.

---

### 🧠 Memory of the Counted Chamber Law

-   **`keyVal`** → O(1) value lookup and update
-   **`keyFreq`** → O(1) frequency lookup and update
-   **`freqKeys[f]`** → ordered list of keys at frequency `f`; front = most recent, back = eviction candidate
-   **`keyIter`** → stored iterator for each key — enables O(1) removal from the middle of its frequency list
-   **`minFreq`** → the eviction pointer — only increases by 1 (in `incrementFreq`) or resets to 1 (on new `put`)
-   **`incrementFreq`:** remove from `freqKeys[f]` → if empty and f==minFreq, minFreq++ → push_front into `freqKeys[f+1]` → update iterator
-   **Eviction target:** `freqKeys[minFreq].back()` — back of the minimum-frequency list = LRU among LFU
-   **New key always enters at frequency 1** → always sets `minFreq = 1`
-   **Time:** O(1) for all get and put operations
-   **Space:** O(capacity) — all four maps grow proportionally to the number of cached keys

Thus is remembered the saga of **LFU Cache**,
where the Keeper maintained four maps in perfect harmony —
knowing every key's value, every key's frequency,
every key's exact position in its frequency list,
and always the minimum frequency in the Chamber —
so that when eviction came,
the most forgotten memory —
visited the fewest times,
and among equals, visited longest ago —
was removed in the blink of an eye,
in constant time,
without a single search. 🏛️📊✨
