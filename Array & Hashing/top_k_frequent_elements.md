## 🔝 _The Summit of Voices: The Top K Frequent Elements Saga_

> _"In the valley each voice is many or few;
> some sing once and fade, others cry loudly, again and again.
> Seek those that thunder most — the few whose echoes shape the hills."_

---

The valley was full of echoes — numbers called and recalled by the townsfolk. Merchants shouted prices, children chanted games, and some numbers rang more often than others. The Council demanded to know: who are the loudest voices? Who are the top `k` freest echoes that shape the day?

Thus began the quest of **Top K Frequent Elements**.

---

### 📜 The Ledger of Echoes

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;
```

The scribes unfurled their tools. The `unordered_map` would count echoes, and a `priority_queue` — a min-heap of size `k` — would keep only the loudest voices, banishing the rest to the quiet valley.

---

### 🔢 The Oracle’s Invocation

```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
```

The Oracle spoke the rite **topKFrequent**. The list of echoes `nums` and the number `k` were placed upon the altar. The hunt for the loudest began.

---

```cpp
    unordered_map<int, int> freq;
```

First, the Ledger of Echoes was opened. Each number’s frequency would be tallied — each shout recorded so the Oracle could measure its volume.

---

```cpp
    for (int x : nums) freq[x]++;
```

One procession after another passed before the Ledger. For each echo `x`, a mark was added. Some tallies swelled quickly — these would be the ones the valley remembered.

---

### 🧾 The Council of Crowns (Min-Heap)

```cpp
    auto cmp = [](pair<int,int>& a, pair<int,int>& b) { return a.second > b.second; };
    priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> minHeap(cmp);
```

To keep the throne-room uncluttered, the Oracle summoned a **min-heap** that kept the `k` loudest voices. Each heap entry was a pair: `{value, frequency}`. The smallest frequency among the kept ones sat at the top — ready to be dethroned if a louder voice arrived.

---

```cpp
    for (auto& p : freq) {
        minHeap.push({p.first, p.second});
        if (minHeap.size() > k) minHeap.pop();
    }
```

For every entry in the Ledger, the Oracle considered crowning it. She pushed the pair into the heap; if the hall had grown beyond `k`, she removed the faintest voice. Thus, only the most resonant `k` remained, while occasional whispers were returned to the crowd.

---

### 🎒 The Gathering of Champions

```cpp
    vector<int> result;
    while (!minHeap.empty()) {
        result.push_back(minHeap.top().first);
        minHeap.pop();
    }
    reverse(result.begin(), result.end()); // optional: from most to less frequent
    return result;
}
```

When the counting ended, the Oracle drew forth the champions from the heap. Popped one by one, they emerged from quietest of the chosen to the loudest — so reversing them yields a proud list ordered from most to least frequent.

---

### 🎺 The Trial of Many Voices

```cpp
int main() {
    vector<int> nums = {1,1,1,2,2,3,3,3,3,4,5,2,2};
    int k = 3;
    vector<int> ans = topKFrequent(nums, k);
    for (int x : ans) cout << x << " ";
    cout << endl;
}
```

In the market, echoes rang: `1` thrice, `2` many times, `3` thundered, `4` and `5` whispered. The Oracle called `k = 3`. From the heap the champions emerged — the three numbers whose voices shaped the valley most.

---

### 🧠 Memory of the Summit

-   **unordered_map\<int,int> freq** → the Ledger counting how often each number shouts.
-   **min-heap of pairs {value,frequency}** → keeps only the top `k` loudest; the smallest frequency among them sits at the top and is removed when necessary.
-   **push then pop if size>k** → the ritual that maintains the throne-room for exactly `k` champions.
-   **pop to result and optional reverse** → gather the champions from quietest-of-top to loudest; reverse if you want descending frequency.

Thus is remembered the saga of the **Top K Frequent Elements**, where only the loudest echoes claim crowns upon the summit. 🔝🗣️
