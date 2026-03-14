## 🏛️🗝️ _The Royal Chamber of Forgotten Memories: The LRU Cache Saga_

> \_"Deep inside the Royal Palace stood a chamber —
> the Chamber of Recent Memories.
>
> It could hold only a fixed number of memories at a time.
> No more. Never more.
>
> When the King needed to recall something,
> the Chamber's Keeper searched within.
> If found — the memory was brought forward,
> moved to the **most honored position** —
> for it had proven itself worthy of being remembered.
>
> If not found — the Keeper fetched it from the vast archives outside,
> placed it in the Chamber,
> and again honored it with the **most recent position**.
>
> But if the Chamber was already full —
> something had to go.
>
> **The memory that had not been visited the longest
> was evicted. Quietly. Ruthlessly.**
>
> This was the Law of the Chamber:
> **Least Recently Used must leave first.**
>
> The Keeper needed two tools to enforce this law in O(1) time:
>
> A **HashMap** — to find any memory instantly by its key.
> A **Doubly Linked List** — to track the order of use,
> with the most recent at one end
> and the least recent at the other.
>
> Together, they formed the perfect system —
> fast lookup, fast reordering, fast eviction.
>
> Every get, every put —
> one operation, constant time.
> The Chamber never overflowed.
> The least remembered always fell first."\_

---

This is the saga of **LRU Cache**.

Design a data structure that follows the
**Least Recently Used (LRU)** cache policy.

Implement the `LRUCache` class:

-   `LRUCache(int capacity)` — initialize with a fixed capacity.
-   `int get(int key)` — return the value if key exists, else `-1`.
    **Move the accessed node to the most recent position.**
-   `void put(int key, int value)` — insert or update the key-value pair.
    **Move it to most recent position.**
    If capacity is exceeded — **evict the least recently used key.**

All operations must run in **O(1)** time.

---

## 🧠 The Oracle's Core Insight — HashMap + Doubly Linked List

To achieve O(1) for both lookup AND ordering:

**HashMap alone** gives O(1) lookup but cannot track order.
**Doubly Linked List alone** tracks order but gives O(n) lookup.

Together they give everything:

```
HashMap:             key → pointer to the node in the list
Doubly Linked List:  nodes ordered by recency of use
                     HEAD ↔ [most recent] ↔ ... ↔ [least recent] ↔ TAIL
```

Two **Ghost Sentinels** — a dummy `head` and a dummy `tail` —
anchor both ends of the list so insertions and deletions
never require checking if a node is at the boundary.

Every operation becomes:

1. Use the HashMap to find the node in O(1).
2. Use the Doubly Linked List to reorder or evict in O(1).

---

### 📜 The Scroll of the Chamber

```cpp
#include <iostream>
#include <unordered_map>
using namespace std;
```

---

### 🧱 The Structure of a Memory Node

```cpp
struct Node {
    int key, val;
    Node* prev;
    Node* next;
    Node(int k, int v) : key(k), val(v), prev(nullptr), next(nullptr) {}
};
```

Each memory node carried a `key` — its name —
and a `val` — its content.

Unlike a singly linked list soldier who only looked forward,
each memory node held **two hands** —
`prev` reaching backward and `next` reaching forward.

This bidirectional grip was the secret
that allowed any node to be removed from the middle
in O(1) without needing to find its predecessor first.

---

## 🏛️ The Chamber is Constructed

_Two Ghost Sentinels. One HashMap. Fixed Capacity._

```cpp
class LRUCache {
    int cap;
    unordered_map<int, Node*> map;
    Node* head;
    Node* tail;
```

-   `cap` — the maximum number of memories allowed.
-   `map` — the **HashMap** — key to node pointer. Instant lookup.
-   `head` — the **Ghost of the Most Recent** — dummy front sentinel.
-   `tail` — the **Ghost of the Least Recent** — dummy back sentinel.

---

```cpp
public:
    LRUCache(int capacity) {
        cap  = capacity;
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head->next = tail;
        tail->prev = head;
    }
```

The Chamber was built.

The two Ghost Sentinels were placed at each end —
facing each other across an empty space.

`head ↔ tail` — the chamber was empty, ready to receive memories.

The most recently used will always live **just after `head`**.
The least recently used will always live **just before `tail`**.

---

## 🔧 Two Private Rituals the Keeper Uses for Everything

_Remove a node. Insert a node just after head. These two moves power all operations._

### ✂️ Ritual One — Remove a Node from Anywhere

```cpp
    void remove(Node* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }
```

The node's predecessor reached past it to grip the successor.
The node's successor reached backward past it to grip the predecessor.

The node was bypassed — silently excised from the chain.
No searching needed. No walking needed.
Both neighbors already knew each other through the node's own hands.

This is why the **doubly linked list** was essential —
`node->prev` gave instant access to the predecessor,
allowing O(1) removal from anywhere in the chain.

---

### 📌 Ritual Two — Insert a Node Just After Head (Most Recent Position)

```cpp
    void insertFront(Node* node) {
        node->next       = head->next;
        node->prev       = head;
        head->next->prev = node;
        head->next       = node;
    }
```

The node was planted in the most honored position —
directly behind the `head` Ghost.

Four pointer updates in precise order:

1. `node->next` grips who was previously first.
2. `node->prev` grips the `head` Ghost.
3. The old first node's `prev` now points back to the new node.
4. `head->next` now points to the new node.

The node was seamlessly woven into the front.

> _"To honor a memory is to move it to the front.
> To forget it is to let it drift to the back."_

---

## 🔍 The GET Operation — Find and Honor

```cpp
    int get(int key) {
        if (map.find(key) == map.end()) return -1;
        Node* node = map[key];
        remove(node);
        insertFront(node);
        return node->val;
    }
```

The Keeper was asked for a memory by `key`.

First — the HashMap was consulted. O(1).

If not found — return `-1`. The memory did not exist.

If found — the node was pulled from wherever it sat in the chain
(`remove`) and planted at the front (`insertFront`) —
honoring it as the most recently used.

Then its value was returned.

> _"A memory that proves its worth
> is moved to the seat of honor.
> A memory that is never asked for
> drifts slowly to the back — and eventually falls."_

---

## 📝 The PUT Operation — Record and Possibly Evict

```cpp
    void put(int key, int value) {
        if (map.find(key) != map.end()) {
            remove(map[key]);
            delete map[key];
        }
```

The Keeper received a new memory to store.

First — if this key **already existed** in the Chamber,
the old node was removed and destroyed.
It would be replaced by a fresh node with the new value.

---

```cpp
        Node* node = new Node(key, value);
        map[key]   = node;
        insertFront(node);
```

A fresh node was forged — carrying the key and new value.

It was registered in the HashMap.
Then planted at the front — the most recently used position.

---

```cpp
        if ((int)map.size() > cap) {
            Node* lru = tail->prev;
            remove(lru);
            map.erase(lru->key);
            delete lru;
        }
    }
};
```

If the Chamber now exceeded capacity —
the **Least Recently Used** node was evicted.

Who is the LRU? The node just before the `tail` Ghost —
the one who has sat untouched the longest,
drifted all the way to the back of the chain.

It was removed from the list,
erased from the HashMap,
and its memory was destroyed.

The Chamber was in balance once more.

---

### 🎺 The Trial of the Royal Chamber

```cpp
int main() {
    LRUCache cache(2); // capacity = 2

    cache.put(1, 1);   // cache: {1=1}
    cache.put(2, 2);   // cache: {1=1, 2=2}
    cout << cache.get(1) << endl;  // returns 1, moves 1 to front
                                   // cache order: [1, 2]
    cache.put(3, 3);   // capacity exceeded — evict LRU = key 2
                       // cache: {1=1, 3=3}
    cout << cache.get(2) << endl;  // returns -1 (evicted)
    cache.put(4, 4);   // capacity exceeded — evict LRU = key 1
                       // cache: {3=3, 4=4}
    cout << cache.get(1) << endl;  // returns -1 (evicted)
    cout << cache.get(3) << endl;  // returns 3
    cout << cache.get(4) << endl;  // returns 4
    return 0;
}
```

The Chamber with capacity `2` was put to trial:

| Operation | Chamber State (front → back) | Result |
| --------- | ---------------------------- | ------ |
| put(1,1)  | [1]                          | —      |
| put(2,2)  | [2, 1]                       | —      |
| get(1)    | [1, 2] ← 1 honored to front  | 1      |
| put(3,3)  | [3, 1] ← 2 evicted (LRU)     | —      |
| get(2)    | [3, 1] ← 2 is gone           | -1     |
| put(4,4)  | [4, 3] ← 1 evicted (LRU)     | —      |
| get(1)    | [4, 3] ← 1 is gone           | -1     |
| get(3)    | [3, 4] ← 3 honored to front  | 3      |
| get(4)    | [4, 3] ← 4 honored to front  | 4      |

The Chamber never overflowed.
The least remembered always fell first.

---

### 🧠 Memory of the Royal Chamber Law

-   **HashMap** (`map`) → O(1) lookup of any key → gives the node pointer directly
-   **Doubly Linked List** → O(1) reordering and eviction → `prev` enables O(1) removal from anywhere
-   **Two Ghost Sentinels** (`head`, `tail`) → no boundary checks ever needed
-   **Most recent** lives just after `head`. **Least recent** lives just before `tail`
-   `remove(node)` = bypass the node by linking its neighbors together
-   `insertFront(node)` = plant the node right after `head` in 4 pointer steps
-   **get:** find via map → remove → insertFront → return value
-   **put:** if exists, remove old → forge new node → insertFront → if over capacity, evict `tail->prev`
-   **Time:** O(1) for both get and put
-   **Space:** O(capacity) — the chamber holds at most `cap` nodes

Thus is remembered the saga of **LRU Cache**,
where the Keeper of the Royal Chamber
wielded a HashMap and a Doubly Linked List
as twin instruments of perfect memory management —
honoring the recently remembered by moving them to the front,
and silently evicting the long-forgotten from the back —
all in the blink of an eye,
all in constant time. 🏛️🗝️✨
