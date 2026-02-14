## 🚗🏁 _The Convergence on the Distant Horizon: The Car Fleet Saga_

> \*"Along the Endless Highway,
> cars raced toward a distant target —
> each with its own position,
> each with its own speed.
>
> The Oracle was commanded:
>
> **‘Count how many fleets
> will arrive at the destination.’**
>
> A fleet was not merely a group —
> it was a destiny.
>
> If a faster car caught a slower one ahead,
> it could not pass.
>
> It slowed down,
> merging into a single moving fate.
>
> The Oracle knew:
> this was not about distance alone —
> but about **arrival time**."\*

---

This is the epic saga of **Car Fleet**.

You are given:

-   `target` → destination point
-   arrays `position[]` and `speed[]`

Each car:

-   starts at `position[i]`
-   moves at constant `speed[i]`
-   moves toward `target`

Your task:

-   Return the **number of fleets**
-   A fleet is a group of cars that arrive together

---

## 🧠 The Oracle’s Core Insight — Arrival Time Decides Fate

For each car:

```
time = (target - position[i]) / speed[i]
```

If a car behind has:

-   **time ≤ time of car ahead**,
    it will catch up → merge into that fleet.

If:

-   **time > time ahead**,
    it forms a new fleet.

Thus:

-   Sort cars by position descending
-   Process from closest to farthest
-   Track the **maximum arrival time seen so far**

---

## ⚔️ The Oracle’s Time-of-Arrival Ritual

```cpp
int carFleet(int target, vector<int>& position, vector<int>& speed) {
    int n = position.size();
    vector<pair<int,double>> cars;
```

Each car would carry:

-   position
-   arrival time

---

### 🧮 Compute Arrival Times

```cpp
    for (int i = 0; i < n; i++) {
        double time = (double)(target - position[i]) / speed[i];
        cars.push_back({position[i], time});
    }
```

The Oracle calculated each destiny.

---

### 🏎️ Sort by Position (Descending)

```cpp
    sort(cars.begin(), cars.end(), greater<>());
```

Closest cars were processed first.

---

### 🏁 Count Fleets

```cpp
    int fleets = 0;
    double maxTime = 0;

    for (auto& car : cars) {
        if (car.second > maxTime) {
            fleets++;
            maxTime = car.second;
        }
    }
    return fleets;
}
```

If a car’s arrival time was greater than
the maximum time seen so far:

-   It formed a **new fleet**

Otherwise:

-   It merged into an existing fleet.

---

### 🎺 The Trial of the Highway

```cpp
int main() {
    int target = 12;
    vector<int> position = {10, 8, 0, 5, 3};
    vector<int> speed = {2, 4, 1, 1, 3};

    cout << carFleet(target, position, speed) << endl;
    // expected: 3
    return 0;
}
```

The Oracle observed:

-   Some cars merged before arrival
-   Others remained separate

Total fleets: **3**

---

### 🧠 Memory of the Fleet Law

-   Compute arrival time for each car
-   Sort by position descending
-   Traverse from closest to farthest
-   If arrival time > maxTime → new fleet
-   Else → merge into previous fleet
-   **Time:** O(n log n)
-   **Space:** O(n)

Thus is remembered the saga of **Car Fleet**,
where the Oracle sees not just speed,
but destiny —
measuring arrival times upon the horizon,
watching as faster cars slow to join slower ones,
until only true fleets remain
marching toward the distant target. 🚗✨
