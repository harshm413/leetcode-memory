## 📧🔗 _The Reunion of Scattered Identities: The Accounts Merge Saga_

> \_"In the Kingdom of Email,
> citizens had accounts --
> each account was a name followed by a list of emails.
>
> But some citizens had MULTIPLE accounts
> under the SAME name.
> And some accounts shared emails --
> meaning they belonged to the SAME person.
>
> The Oracle was commanded:
>
> **'Merge all accounts that belong to the same person.
> Two accounts belong to the same person
> if they share at least ONE email.
> Return the merged accounts,
> each with the name and ALL their emails sorted.'**
>
> The Oracle saw this as a **Union-Find** problem.
>
> Every email was a node.
> If two emails appeared in the same account --
> they belonged to the same person.
> Union them.
>
> After processing all accounts,
> group emails by their root (Union-Find leader).
> Each group = one person's merged account.
>
> Map each root back to the person's name.
> Sort the emails. Done."\_

---

This is the saga of **Accounts Merge**.

Given a list of `accounts` where:
-   `accounts[i][0]` = name
-   `accounts[i][1], accounts[i][2], ...` = emails

Two accounts belong to the same person
if they share **at least one email**.

Return merged accounts: `[name, email1, email2, ...]`
with emails **sorted**.

```
Input:
  [["John","john@mail","john_work@mail"],
   ["John","john@mail","john_home@mail"],
   ["Mary","mary@mail"],
   ["John","johnny@mail"]]

Output:
  [["John","john@mail","john_home@mail","john_work@mail"],
   ["Mary","mary@mail"],
   ["John","johnny@mail"]]
```

The first two accounts share `"john@mail"` → merged.
The last "John" has no shared email → separate person.

---

## 🧠 The Oracle's Core Insight -- Union Emails, Not Accounts

The key realization:

> **Emails are the nodes. Accounts are the edges.**

Within each account, all emails belong to the same person.
So union the first email with every other email in that account.

After all unions, emails with the same root = same person.

```
1. Assign each unique email an ID (index).
2. For each account: union all its emails together
   (union email[1] with email[2], email[1] with email[3], etc.)
3. Group emails by their Union-Find root.
4. Map each root to the account name.
5. Sort emails within each group.
```

```
Time:  O(N × K × α(NK)) where N = accounts, K = avg emails per account
Space: O(NK)
```

---

### 📜 The Scroll of the Scattered Identities

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <unordered_map>
#include <map>
#include <algorithm>
using namespace std;
```

---

## 🏗️ The Union-Find Structure

```cpp
class UnionFind {
    vector<int> parent, rank;
public:
    UnionFind(int n) : parent(n), rank(n, 0) {
        for (int i = 0; i < n; i++) parent[i] = i;
    }
```

Every email ID starts as its own parent.

---

### 🔍 Find with Path Compression

```cpp
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }
```

Follow the chain to the root. Flatten along the way.

---

### 🔗 Union by Rank

```cpp
    void unite(int a, int b) {
        int ra = find(a), rb = find(b);
        if (ra == rb) return;
        if (rank[ra] < rank[rb]) swap(ra, rb);
        parent[rb] = ra;
        if (rank[ra] == rank[rb]) rank[ra]++;
    }
};
```

Shorter tree goes under taller tree.
Same height → rank goes up by 1.

---

## 📧 Step One -- Assign Each Email a Unique ID

```cpp
vector<vector<string>> accountsMerge(vector<vector<string>>& accounts) {
    unordered_map<string, int> emailToId;
    unordered_map<string, string> emailToName;
    int id = 0;
```

Two maps:

-   `emailToId` -- maps each email string to a unique integer ID.
    Union-Find works on integers, not strings.

-   `emailToName` -- maps each email to the account holder's name.
    We'll need this later to attach names to merged groups.

`id` is a running counter for assigning IDs.

---

```cpp
    for (auto& account : accounts) {
        string name = account[0];
```

The first element of each account is the name.

---

```cpp
        for (int i = 1; i < (int)account.size(); i++) {
            string email = account[i];

            if (!emailToId.count(email)) {
                emailToId[email] = id++;
            }
            emailToName[email] = name;
        }
    }
```

For each email in the account:

If it's a **new email** → assign it a fresh ID.
If it's already seen → it already has an ID (shared between accounts).

Map every email to the account holder's name.
If the same email appears under different account entries,
the name will be the same (problem guarantees this).

> _"Every email gets a number.
> Every email remembers whose name it carries.
> The numbers are what Union-Find will work with."_

---

## 🔗 Step Two -- Union All Emails Within Each Account

```cpp
    UnionFind uf(id);

    for (auto& account : accounts) {
        int firstId = emailToId[account[1]];
```

Create a Union-Find with `id` total email IDs.

For each account, grab the ID of the **first email**.
All other emails in this account will be unioned with it.

---

```cpp
        for (int i = 2; i < (int)account.size(); i++) {
            uf.unite(firstId, emailToId[account[i]]);
        }
    }
```

Union the first email's ID with every other email's ID
in the same account.

After this, all emails in the same account share the same root.
And if two accounts share an email --
their email sets are transitively merged.

> _"Within each account, all emails are kin.
> Union them through the first email.
> If account A and account B share an email,
> that shared email bridges the two groups --
> Union-Find merges them automatically."_

---

## 📦 Step Three -- Group Emails by Their Root

```cpp
    map<int, vector<string>> groups;

    for (auto& [email, eid] : emailToId) {
        int root = uf.find(eid);
        groups[root].push_back(email);
    }
```

For every email, find its Union-Find root.
Group all emails with the same root together.

Each group = one person's complete set of emails.

`map` (not `unordered_map`) is used here for deterministic ordering,
but `unordered_map` works too.

---

## 📋 Step Four -- Build the Result

```cpp
    vector<vector<string>> result;

    for (auto& [root, emails] : groups) {
```

Walk through each group.

---

### 🔤 Sort the Emails

```cpp
        sort(emails.begin(), emails.end());
```

The problem requires emails in sorted order within each account.

---

### 📝 Prepend the Name

```cpp
        string name = emailToName[emails[0]];
        emails.insert(emails.begin(), name);
```

Look up the name from any email in the group
(they all map to the same name).

Insert the name at the front of the email list.

---

### 📥 Add to Result

```cpp
        result.push_back(emails);
    }
    return result;
}
```

The merged account was added to the result.

---

### 🎺 The Trial of the Scattered Identities

```cpp
int main() {
    vector<vector<string>> accounts = {
        {"John", "john@mail", "john_work@mail"},
        {"John", "john@mail", "john_home@mail"},
        {"Mary", "mary@mail"},
        {"John", "johnny@mail"}
    };

    auto result = accountsMerge(accounts);
    for (auto& acc : result) {
        for (auto& s : acc) cout << s << " ";
        cout << endl;
    }
    return 0;
}
```

---

**Full trace:**

**Step 1 -- Assign IDs:**

| Email           | ID | Name |
| --------------- | -- | ---- |
| john@mail       | 0  | John |
| john_work@mail  | 1  | John |
| john_home@mail  | 2  | John |
| mary@mail       | 3  | Mary |
| johnny@mail     | 4  | John |

**Step 2 -- Union within each account:**

Account 1: `["John", "john@mail", "john_work@mail"]`
→ union(0, 1). Sets: {0,1}, {2}, {3}, {4}.

Account 2: `["John", "john@mail", "john_home@mail"]`
→ union(0, 2). Sets: {0,1,2}, {3}, {4}.

Account 3: `["Mary", "mary@mail"]`
→ only one email. No union needed.

Account 4: `["John", "johnny@mail"]`
→ only one email. No union needed.

**Step 3 -- Group by root:**

| Email           | find(ID) | Root |
| --------------- | -------- | ---- |
| john@mail       | 0        | 0    |
| john_work@mail  | 0        | 0    |
| john_home@mail  | 0        | 0    |
| mary@mail       | 3        | 3    |
| johnny@mail     | 4        | 4    |

Groups:
-   Root 0: `["john@mail", "john_home@mail", "john_work@mail"]`
-   Root 3: `["mary@mail"]`
-   Root 4: `["johnny@mail"]`

**Step 4 -- Sort and prepend names:**

```
["John", "john@mail", "john_home@mail", "john_work@mail"]
["Mary", "mary@mail"]
["John", "johnny@mail"]
```

**Answer matches expected output** ✓

---

**Why the two "John" accounts merged but the third didn't:**

Accounts 1 and 2 both contain `"john@mail"`.
When account 2 was processed, `emailToId["john@mail"]` already existed (ID 0).
Union(0, 2) merged the two accounts' email sets.

Account 4 (`"johnny@mail"`) shares NO email with accounts 1 or 2.
It stays separate -- a different John (or the same John with an unlinked account).

> _"Names don't determine identity. Emails do.
> Two Johns with no shared email are strangers.
> Two Johns with one shared email are the same person."_

---

## 🔍 Why Union the First Email with All Others?

Within one account: `[name, e1, e2, e3]`.

We union `e1` with `e2`, and `e1` with `e3`.
This connects all three through `e1`.

We DON'T need to union `e2` with `e3` directly --
they're already connected through `e1` (transitivity).

Using the first email as the "hub" minimizes union calls
from O(K²) to O(K) per account.

---

## 🔍 Why Not DFS/BFS?

You COULD build a graph where emails are nodes
and edges connect emails in the same account,
then DFS to find connected components.

That works. But Union-Find is cleaner here because:
-   No adjacency list needed.
-   Merging happens naturally as edges are processed.
-   No visited array needed.

Both are O(NK). Union-Find is more elegant for this problem.

---

### 🧠 Memory of the Scattered Identities Law

-   **Emails are nodes. Accounts are edges.**
-   **Step 1:** assign each email a unique integer ID + map email→name
-   **Step 2:** for each account, union first email's ID with all others
-   **Step 3:** group emails by Union-Find root
-   **Step 4:** sort emails, prepend name, collect result
-   **Shared email** between accounts → Union-Find merges them transitively
-   **Names don't determine identity** -- only shared emails do
-   **Union first with all others** → O(K) per account, not O(K²)
-   **Time:** O(NK × α(NK)) ≈ O(NK)
-   **Space:** O(NK)
-   **Edge cases:**
    -   Single email per account → no merging
    -   All accounts share one email → all merge into one
    -   Same name, no shared emails → separate accounts

Thus is remembered the saga of **Accounts Merge**,
where the Oracle did not compare names
but instead united emails --
assigning each a number,
unioning all emails within each account,
letting shared emails bridge separate accounts --
until every person's scattered identities
were gathered under one roof,
their emails sorted,
their name restored. 📧🔗✨
