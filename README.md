
```markdown
# 📦 Warehouse Picklist Optimization Engine

**Team:** Bit Musketeers  
**Domain:** Supply Chain Logistics / Operations Research

![Python](https://img.shields.io/badge/Python-3.8%2B-blue) ![Algorithm](https://img.shields.io/badge/Algorithm-Greedy%20Heuristic-green) ![Status](https://img.shields.io/badge/Status-Hackathon%20Submission-orange)

## 📖 Overview

This project implements a high-performance **Simulation-Based Heuristic Engine** designed to maximize warehouse fulfillment throughput. The system processes large-scale order data to generate optimized picklists while strictly adhering to complex operational constraints (picker availability, weight limits, shift timings, and zone restrictions).

Our solution prioritizes **Effective Fulfillment**—ensuring SKUs are picked before their critical cutoff times—achieving an **O(N log N)** time complexity suitable for millions of records.

---

## 🚀 Key Features

- **Time-Synchronized Operations:** - Aligns all operations to a global **21:00 (9 PM)** start time.
  - Automatically schedules shifts ending before the start time (e.g., Morning Shift) to the subsequent calendar day to ensure 100% workforce utilization.
  
- **Dynamic Resource Management:** - Uses a **Min-Heap (Priority Queue)** to manage a pool of **150 pickers** across overlapping shifts.
  - Assigns tasks dynamically to the picker who becomes available earliest.

- **Multi-Constraint Batching:**
  - **Zone Integrity:** Strict one-zone-per-picklist rule.
  - **Capacity Limits:** Max 2,000 units per picklist.
  - **Weight Protocol:** 200kg (Standard) vs. 50kg (Fragile).
  - **Store Diversity:** Controlled by `pods_per_picklist` limits.

- **Wasted Effort Mitigation:** - Predictive logic calculates the "Finish Time" *before* assignment.
  - If a task cannot be completed before the SKU cutoff or picker's shift end, it is dropped to preserve efficiency for viable orders.

---

## 🧠 Algorithmic Approach

### Why We Chose a Heuristic over Reinforcement Learning
During our R&D phase, we explored **Reinforcement Learning (RL)** and **Google OR-Tools**. However, we pivoted to a custom Simulation Heuristic for the following reasons:

1.  **State Space Explosion:** With thousands of SKUs and 150 concurrent agents (pickers), the branching factor for an RL agent was too massive to converge within the hackathon timeframe.
2.  **Feasibility & Scale:** Constraint Programming solvers (like OR-Tools) struggled to optimize efficiently across millions of rows with non-linear travel time calculations.
3.  **Deterministic Stability:** Our simulation approach provides a robust, explainable, and highly scalable solution that guarantees hard constraint adherence.

### The Solution: Priority-Queue Simulation
1.  **Heuristic Sorting:** The global order book is prioritized by:
    `Cutoff_Time` (Earliest Deadline) → `Zone` (Clustering) → `Bin_Rank` (Travel Minimization).
2.  **Greedy Assignment:** The engine pulls the most urgent valid batch and assigns it to the first available picker from the Heap.
3.  **Travel Modeling:**
    $$T_{total} = T_{setup} + (N_{bins} \times 0.5) + (N_{units} \times T_{pick}) + (N_{orders} \times 0.5) + T_{staging}$$

---

## 📊 Performance Metrics

On the provided test dataset, our engine achieved:

| Metric | Value |
| :--- | :--- |
| **Total SKU Units Picked** | **48,906** |
| **Total Picklists Generated** | **4,693** |
| **Picker Utilization** | **100%** (150 Pickers) |
| **Algorithm Speed** | High (Vectorized Processing) |

---

## 🛠️ Installation & Usage

### Prerequisites
* Python 3.8+
* Pandas, NumPy

```bash
pip install pandas numpy

```

### Configuration

1. Place your dataset in the project folder.
2. Update the `INPUT_FILE` path in `main.py`:
```python
INPUT_FILE = '/content/picklist_data.csv'

```


3. (Optional) Adjust warehouse physics constants in `config`:
* `CUTOFF_MAP`: Priority-to-time mapping.
* `TIME_PARAMS`: Operational costs (walking speed, picking time).



### Running the Engine

```bash
python main.py

```

### Output

The script generates:

1. `picklists/`: A folder containing individual CSVs for every generated picklist.
2. `Summary.csv`: A master log of all created picklists and their metadata.

---

## 📂 Project Structure

```
├── main.py                 # Core optimization logic
├── picklist_data.csv       # Input dataset (Ignored in git)
├── picklists/              # Output directory for generated lists
├── Summary.csv             # Final performance report
└── README.md               # Project documentation

```

---

**Built by Team Bit Musketeers**

```

```
