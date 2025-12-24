# Warehouse Picklist Optimization Engine

**Team:** Bit Musketeers

## 1. Project Overview

This project addresses a complex, large-scale constrained scheduling problem for warehouse operations. The goal is to maximize **Effective Fulfillment** (SKUs picked before their cutoff times) while strictly adhering to operational constraints such as picker availability, shift timings, weight limits, and zone restrictions.

Our solution implements a high-performance **Simulation-Based Heuristic Engine** capable of processing millions of rows to generate optimized picklists in  time.

## 2. Key Features

* **Dynamic Resource Management:** Utilizes a Min-Heap (Priority Queue) to manage a pool of 150 pickers across overlapping shifts (Night, Morning, General).
* **Time-Synchronized Operations:** Global operational start time is synchronized to **21:00 (9 PM)**. Morning shifts ending before this time are automatically scheduled for the subsequent calendar day to ensure full workforce utilization.
* **Multi-Constraint Batching:** Strictly enforces four concurrent constraints per picklist:
* **Zone Integrity:** One zone per picklist.
* **Capacity Limits:** Max 2000 units.
* **Weight Protocol:** 200kg (Standard) vs. 50kg (Fragile).
* **Store Diversity:** Limited by `pods_per_picklist` parameter.


* **Wasted Effort Mitigation:** Predictive logic calculates "Finish Time" before assignment. If a task cannot be completed before the SKU cutoff or shift end, it is skipped to preserve efficiency.

## 3. Algorithmic Approach

We employ a **Priority-Queue Simulation** rather than a standard optimization solver to handle the scale of data.

1. **Heuristic Sorting:** The global order book is sorted by `Cutoff_Time`  `Zone`  `Bin_Rank`. This prioritizes urgent deadlines while maximizing picking density to reduce travel time.
2. **Picker Assignment:** A Min-Heap tracks the `available_time` of every picker. The engine greedily assigns the highest-priority batch to the picker who becomes free earliest.
3. **Travel Modeling:** Time cost is calculated dynamically:



## 4. Design Decisions: Why Not Reinforcement Learning?

During the initial R&D phase, we attempted to solve this problem using **Reinforcement Learning (RL)** and **Google OR-Tools**. However, we pivoted to the current Heuristic approach for the following reasons:

* **State Space Explosion:** With thousands of SKUs, 150 distinct agents (pickers), and continuous time variables, the branching factor for an RL agent was too massive to converge within a reasonable timeframe.
* **Scalability:** Constraint Programming solvers (like OR-Tools) struggled to handle the sheer volume of data (millions of rows) alongside the complex, non-linear constraints of travel time calculations.
* **Feasibility:** The Simulation-Based Heuristic provides a robust, deterministic, and highly scalable solution that guarantees adherence to hard constraints while achieving near-optimal throughput.

## 5. Performance & Results

Our engine achieved the following metrics on the test dataset:

| Metric | Result |
| --- | --- |
| **Total SKU Units Picked** | **48,906.0** |
| Total Picklists Generated | 4,693 |
| Picker Utilization | 100% (150 Pickers) |

## 6. Setup & Usage

### Prerequisites

* Python 3.8+
* Pandas, NumPy

```bash
pip install pandas numpy

```

### Configuration

Update the `INPUT_FILE` path in the script to point to your dataset:

```python
INPUT_FILE = 'path/to/your_data.csv'

```

### Operational Constants

You can adjust the warehouse physics in the configuration section of the script:

* `CUTOFF_MAP`: Priority-based cutoff times.
* `TIME_PARAMS`: Costs for setup, travel, and picking.
* `SHIFTS`: Definitions for shift start/end times and headcount.

### Running the Optimizer

```bash
python optimize_warehouse.py

```

## 7. Output

The script generates:

1. **`Summary.csv`**: A high-level log of all generated picklists.
2. **`picklists/`**: A directory containing individual CSV files for every generated picklist, ready for the warehouse management system (WMS).
