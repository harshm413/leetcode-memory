## �🏰 _The Tower of Loudest Voices: The Maximum Frequency Stack Saga_

> \_"In the Kingdom of Frequencies,
> numbers arrived one by one,
> each adding their voice to the chorus.
>
> Some numbers spoke once and fell silent.
> Others returned again and again,
> their frequency growing with every appearance.
>
> The King issued a strange decree:
>
> **'When I command POP,
> remove the element with the HIGHEST frequency.
> If multiple elements share the highest frequency,
> remove the one that was pushed MOST RECENTLY
> among them.'**
>
> The Architect thought carefully.
>
> A max-heap could track frequencies --
> but breaking ties by recency was tricky.
>
> Then the Architect saw a more elegant design:
>
> **Group elements by frequency.
> Frequency 1 has its own stack.
> Frequency 2 has its own stack.
> Frequency 3 has its own stack.
> And so on.**
>
> **When an element is pushed,
> increment its frequency
> and push it onto the stack for that frequency.**
>
> **When pop is called,
> pop from the stack at the current maximum frequency.
> If that stack becomes empty, decrement maxFreq.**
>
> The stacks naturally handled recency --
> the most recently pushed element at any frequency
> sat on top of that frequency's stack.
>
> A frequency map. A group of stacks. One maxFreq counter.
> O(1) for everything."\_

---

This is the saga of **Maximum Frequency Stack**.

Design a stack-like data structure:

-   `push(val)` -- push an integer onto the stack.
-   `pop()` -- remove and return the **most frequent** element.
    If tied, return the one **most recently pushed**.

```
push(5), push(7), push(5), push(7), push(4), push(5)

pop() → 5  (freq 3, most frequent)
pop() → 7  (freq 2, tied with 5 at freq 2, but 7 was pushed more recently)
pop() → 5  (freq 2, now most frequent)
pop() → 4  (freq 1, tied with 5 and 7, but 4 was pushed most recently)
```

---

## 🧠 The Oracle's Core Insight -- Frequency-Grouped Stacks

The key data structures:

```
freq[val]        → current frequency of val
groupStack[f]    → stack of all elements that have been pushed at frequency f
maxFreq          → the current maximum frequency
```

**Push(val):**

```
freq[val]++
f = freq[val]
groupStack[f].push(val)
maxFreq = max(maxFreq, f)
```

An element with frequency 3 exists in groupStack[1], groupStack[2], AND groupStack[3].
It was pushed into each level as its frequency grew.

**Pop():**

```
val = groupStack[maxFreq].top()
groupStack[maxFreq].pop()
freq[val]--
if groupStack[maxFreq] is empty → maxFreq--
return val
```

Pop from the highest frequency stack.
The top of that stack is the most recently pushed element at that frequency --
automatically breaking ties by recency.

```
Time:  O(1) for both push and pop
Space: O(N) -- all elements stored across frequency stacks
```

---

### 📜 The Scroll of the Frequency Kingdom

```cpp
#include <iostream>
#include <stack>
#include <unordered_map>
using namespace std;
```

---

## 🏗️ The Frequency Tower Is Built

```cpp
class FreqStack {
    unordered_map<int, int> freq;
    unordered_map<int, stack<int>> groupStack;
    int maxFreq;
```

Three instruments:

-   `freq[val]` -- how many times `val` has been pushed (minus pops).
-   `groupStack[f]` -- a stack of all elements pushed at frequency level `f`.
-   `maxFreq` -- the current highest frequency across all elements.

---

```cpp
public:
    FreqStack() : maxFreq(0) {}
```

The tower began empty. No frequencies. No stacks. Max frequency = 0.

---

## 📥 Push -- Increment Frequency, Add to Group

```cpp
    void push(int val) {
        freq[val]++;
        int f = freq[val];
        groupStack[f].push(val);
        maxFreq = max(maxFreq, f);
    }
```

The element's frequency was incremented.

It was then pushed onto the stack for its **new** frequency level.

If this was the highest frequency seen so far, `maxFreq` was updated.

> _"An element pushed for the 3rd time
> lives in groupStack[1], groupStack[2], AND groupStack[3].
> Each level remembers when it arrived at that frequency.
> This is not duplication -- it is history."_

---

## 📤 Pop -- Remove from the Highest Frequency Stack

```cpp
    int pop() {
        int val = groupStack[maxFreq].top();
        groupStack[maxFreq].pop();
        freq[val]--;
```

The Oracle reached into the stack at `maxFreq` --
the highest frequency level.

The top element was the most recently pushed
among all elements at that frequency.
This naturally broke ties by recency.

Its frequency was decremented --
it still existed at lower frequency levels.

---

```cpp
        if (groupStack[maxFreq].empty()) {
            maxFreq--;
        }
        return val;
    }
};
```

If popping emptied the stack at `maxFreq`,
the maximum frequency dropped by 1.

Why only by 1? Because frequencies are built incrementally.
If frequency 3 exists, frequency 2 must also exist
(the same element was pushed through levels 1, 2, 3).
So `maxFreq - 1` is always a valid level.

> _"The tower never skips a floor.
> If the top floor empties,
> the floor below is guaranteed to exist."_

---

### 🎺 The Trial of the Frequency Tower

```cpp
int main() {
    FreqStack fs;
    fs.push(5); fs.push(7); fs.push(5);
    fs.push(7); fs.push(4); fs.push(5);

    cout << fs.pop() << endl; // 5
    cout << fs.pop() << endl; // 7
    cout << fs.pop() << endl; // 5
    cout << fs.pop() << endl; // 4
    return 0;
}
```

---

**Full trace of pushes:**

| Operation | freq after       | groupStack after                          | maxFreq |
| --------- | ---------------- | ----------------------------------------- | ------- |
| push(5)   | {5:1}            | {1:[5]}                                   | 1       |
| push(7)   | {5:1, 7:1}      | {1:[5,7]}                                 | 1       |
| push(5)   | {5:2, 7:1}      | {1:[5,7], 2:[5]}                          | 2       |
| push(7)   | {5:2, 7:2}      | {1:[5,7], 2:[5,7]}                        | 2       |
| push(4)   | {5:2, 7:2, 4:1} | {1:[5,7,4], 2:[5,7]}                      | 2       |
| push(5)   | {5:3, 7:2, 4:1} | {1:[5,7,4], 2:[5,7], 3:[5]}              | 3       |

**Full trace of pops:**

| Operation | maxFreq | groupStack[maxFreq] top | Popped | freq after       | maxFreq after |
| --------- | ------- | ----------------------- | ------ | ---------------- | ------------- |
| pop()     | 3       | 5                       | **5**  | {5:2, 7:2, 4:1} | 2 (3 empty)   |
| pop()     | 2       | 7                       | **7**  | {5:2, 7:1, 4:1} | 2             |
| pop()     | 2       | 5                       | **5**  | {5:1, 7:1, 4:1} | 1 (2 empty)   |
| pop()     | 1       | 4                       | **4**  | {5:1, 7:1, 4:0} | 1             |

**Answers: 5, 7, 5, 4** ✓

First pop: `5` had frequency 3 (highest). Popped from groupStack[3].
Second pop: `5` and `7` both at frequency 2. `7` was pushed more recently at level 2. Popped `7`.
Third pop: `5` alone at frequency 2. Popped.
Fourth pop: all at frequency 1. `4` was pushed most recently at level 1. Popped.

---

**Trace for `push(1), push(1), push(1), pop(), pop(), push(1), pop()`:**

| Operation | freq   | groupStack                | maxFreq | Result |
| --------- | ------ | ------------------------- | ------- | ------ |
| push(1)   | {1:1}  | {1:[1]}                   | 1       | --     |
| push(1)   | {1:2}  | {1:[1], 2:[1]}            | 2       | --     |
| push(1)   | {1:3}  | {1:[1], 2:[1], 3:[1]}     | 3       | --     |
| pop()     | {1:2}  | {1:[1], 2:[1]}            | 2       | **1**  |
| pop()     | {1:1}  | {1:[1]}                   | 1       | **1**  |
| push(1)   | {1:2}  | {1:[1], 2:[1]}            | 2       | --     |
| pop()     | {1:1}  | {1:[1]}                   | 1       | **1**  |

The same element pushed and popped repeatedly.
Frequency rises and falls correctly.

---

## 🔍 Why Not a Max-Heap?

A max-heap keyed on `(frequency, push_order)` would also work --
but `push` and `pop` would be O(log N) due to heap operations.

The frequency-grouped stacks achieve O(1) for both operations
because:
-   `push` is just a map increment + stack push.
-   `pop` is just a stack pop + map decrement.
-   `maxFreq` only changes by ±1, never needs searching.

---

### 🧠 Memory of the Frequency Tower Law

-   **`freq[val]`** -- current frequency of each value
-   **`groupStack[f]`** -- stack of elements at frequency level `f`
-   **`maxFreq`** -- current highest frequency
-   **Push:** `freq[val]++`, push to `groupStack[freq[val]]`, update `maxFreq`
-   **Pop:** pop from `groupStack[maxFreq]`, `freq[val]--`, if stack empty → `maxFreq--`
-   Elements exist at **multiple** frequency levels simultaneously
-   `maxFreq` only decrements by 1 -- no floor is ever skipped
-   Stack at each level handles **recency** naturally (LIFO)
-   **Time:** O(1) for both push and pop
-   **Space:** O(N) -- elements stored across frequency stacks

Thus is remembered the saga of **Maximum Frequency Stack**,
where the Architect built a tower of frequency-grouped stacks --
each floor holding the elements that reached that frequency,
ordered by recency of arrival --
so that when the King commanded POP,
the loudest voice on the highest floor
was silenced first,
and when that floor emptied,
the tower simply descended one level --
always O(1), always correct,
the loudest and most recent falling first. 🍟🏰✨
