# IT 2010 – Introduction to Optimization Algorithms
### Complete Study Guide: All Tasks Explained from Scratch

> **Who is this for?** Someone with zero background in math, programming, or optimization.  
> Every concept is explained from the ground up before the task is solved.

---

## Table of Contents

1. [What is Mathematical Optimization?](#1-what-is-mathematical-optimization)
2. [Task: Production Planning (U and V products)](#2-task-production-planning-u-and-v-products)
3. [Task: Solving Optimization with Pyomo (Python)](#3-task-solving-optimization-with-pyomo-python)
4. [Task: Linear Duality – Building Microchips Optimality Certificate](#4-task-linear-duality--building-microchips-optimality-certificate)
5. [Task: Finding the Dual of a General LO Problem](#5-task-finding-the-dual-of-a-general-lo-problem)
6. [Task: Sales Package Optimization (Phones Example)](#6-task-sales-package-optimization-phones-example)
7. [Task: BigFarm – Minimize Crop Growing Cost](#7-task-bigfarm--minimize-crop-growing-cost)
8. [Task: Mixed-Integer Optimization – Building Microchips with Waste](#8-task-mixed-integer-optimization--building-microchips-with-waste)
9. [Task: Knapsack Problem (Resource Allocation)](#9-task-knapsack-problem-resource-allocation)
10. [Task: MILO Modeling Techniques (Logical Constraints)](#10-task-milo-modeling-techniques-logical-constraints)
11. [Task: Branch and Bound – Solving a MILO Problem](#11-task-branch-and-bound--solving-a-milo-problem)
12. [Task: Network Optimization – Dinner Seating Arrangement](#12-task-network-optimization--dinner-seating-arrangement)
13. [Task: Transport Logistics as a Network Problem](#13-task-transport-logistics-as-a-network-problem)
14. [Task: Shortest Path – Dijkstra's Algorithm](#14-task-shortest-path--dijkstras-algorithm)
15. [Task: Transportation Problem – Gasoline Distribution](#15-task-transportation-problem--gasoline-distribution)
16. [Task: Rebalancing a Bike-Sharing System](#16-task-rebalancing-a-bike-sharing-system)
17. [Task: Max-Weight Bipartite Matching](#17-task-max-weight-bipartite-matching)
18. [Task: Maximum Matching and Max-Flow / Min-Cut](#18-task-maximum-matching-and-max-flow--min-cut)
19. [Task: Traveling Salesman Problem (TSP)](#19-task-traveling-salesman-problem-tsp)
20. [Task: Graph Coloring](#20-task-graph-coloring)
21. [Task: Arbitrage Search in Cryptocurrency Markets](#21-task-arbitrage-search-in-cryptocurrency-markets)
22. [Task: Convex Optimization – Milk Pooling and Blending](#22-task-convex-optimization--milk-pooling-and-blending)
23. [Task: Center of Mass of a Glass of Water](#23-task-center-of-mass-of-a-glass-of-water)
24. [Task: Ordinary Least Squares Regression](#24-task-ordinary-least-squares-regression)
25. [Task: Markowitz Portfolio Optimization](#25-task-markowitz-portfolio-optimization)
26. [Task: KKT Conditions and Duality for Convex Optimization](#26-task-kkt-conditions-and-duality-for-convex-optimization)
27. [Task: Support Vector Machines (SVM) for Binary Classification](#27-task-support-vector-machines-svm-for-binary-classification)
28. [Task: Economic Order Quantity – Conic Optimization](#28-task-economic-order-quantity--conic-optimization)

---

## 1. What is Mathematical Optimization?

### The Big Idea

Optimization means finding the **best possible answer** to a problem. In real life, "best" could mean:
- Maximum profit
- Minimum cost
- Fastest delivery
- Least waste

The challenge is that you usually have **limitations** (not enough money, time, materials, workers) and you need to find the best solution *within those limits*.

### The Three Building Blocks

Every optimization problem has exactly three parts:

| Part | What it is | Example |
|---|---|---|
| **Decision Variables** | The things you can control / choose | How many products to make |
| **Objective Function** | What you want to maximize or minimize | Total profit |
| **Constraints** | The rules/limits that must be respected | You only have 100 hours of labor |

### Linear vs Non-Linear

- **Linear** means all the relationships are straight lines (proportional). If one unit earns $5, then ten units earn $50. Linear problems are generally easy to solve.
- **Non-linear** means some relationships curve. These can be harder.

### Feasible Set

The **feasible set** is the collection of all possible solutions that satisfy the constraints. Your job is to find the *best* point inside this set.

### Global Optimum

The **global optimum** is the single best solution among all feasible solutions. It's what you're always hunting for.

---

## 2. Task: Production Planning (U and V products)

### Background Concepts

Before tackling the math, understand this: a company makes two products. Every product needs some **raw materials** and **labor**. The company wants to **make as much profit as possible** but is limited by how much raw material and labor it has.

### The Problem

A small company makes two products:

- **Product U** (higher-priced):  
  - Sells for **$270** per unit  
  - Needs **10 g** of raw material  
  - Needs **1 hour** of Labor A  
  - Needs **2 hours** of Labor B  
  - Market demand is **limited to 40 units/week** (can't sell more than 40)

- **Product V** (lower-priced):  
  - Sells for **$210** per unit  
  - Needs **9 g** of raw material  
  - Needs **1 hour** of Labor A  
  - Needs **1 hour** of Labor B  
  - Demand is **unlimited**

- Raw material costs **$10 per gram**  
- Unused raw material is discarded at the end of the week (it expires)

Resources available each week (exact limits depend on the table in the slides, but the structure is):
- A limited amount of Labor A (hours)
- A limited amount of Labor B (hours)
- A budget for raw materials

**Goal:** How many units of U and V to produce to **maximize gross profit**?

### How to Solve It

**Step 1 – Define decision variables.**  
These are the quantities we choose:

- `u` = number of units of Product U to produce per week
- `v` = number of units of Product V to produce per week
- `r` = grams of raw material to order per week

**Step 2 – Write the objective function.**  
Profit = Revenue from U + Revenue from V − Cost of raw material

```
Maximize: 270·u + 210·v − 10·r
```

Every unit of U earns $270, every unit of V earns $210. We subtract the raw material cost ($10 per gram).

**Step 3 – Write the constraints.**

```
10·u + 9·v ≤ r         (raw material used cannot exceed what was ordered)
u + v ≤ [max Labor A hours]   (total labor A hours used)
2·u + v ≤ [max Labor B hours] (total labor B hours used)
u ≤ 40                 (market demand cap for U)
u ≥ 0, v ≥ 0, r ≥ 0   (can't produce negative amounts)
```

**Step 4 – Interpret the solution.**  
A solver (or graphical method) finds the values of `u`, `v`, and `r` that give the highest profit while satisfying all constraints.

### Why This Works

Because all relationships are linear (profit grows proportionally with production), this is a **Linear Optimization (LO)** problem. LO problems can always be solved to find the guaranteed best answer.

---

## 3. Task: Solving Optimization with Pyomo (Python)

### Background Concepts

**What is a solver?**  
A solver is a computer program that takes your optimization model (the variables, objective, constraints) and finds the best solution automatically. You don't manually calculate — you describe the problem, and the solver does the heavy lifting.

**What is Pyomo?**  
Pyomo is a Python library (a set of tools) that lets you describe optimization problems in code, and then send them to a solver like HiGHS or CBC.

**What is HiGHS?**  
HiGHS is a free, open-source solver. It's very fast for linear and mixed-integer problems.

### The Task

Take the Production Planning problem (Task 2) and implement it in Pyomo so a computer can solve it automatically.

### How to Solve It – Step by Step

**Step 1: Install Pyomo and HiGHS**
```bash
pip install pyomo highspy
```

**Step 2: Import Pyomo**
```python
from pyomo.environ import *
```
This loads all the Pyomo tools you'll need.

**Step 3: Create the Model Object**
```python
model = ConcreteModel()
```
Think of this as creating an empty container where you'll put your variables, objective, and constraints.

**Step 4: Define Decision Variables**
```python
model.u = Var(within=NonNegativeReals, bounds=(0, 40))  # units of U (max 40)
model.v = Var(within=NonNegativeReals)                   # units of V
model.r = Var(within=NonNegativeReals)                   # grams of raw material
```
`within=NonNegativeReals` means the variable can't go below zero.

**Step 5: Define Expressions (optional helper quantities)**
```python
# Example: total revenue
model.revenue = Expression(expr=270*model.u + 210*model.v)
```

**Step 6: Define the Objective**
```python
model.profit = Objective(
    expr=270*model.u + 210*model.v - 10*model.r,
    sense=maximize
)
```
`sense=maximize` tells the solver we want the biggest possible value.

**Step 7: Define Constraints**
```python
model.raw_material = Constraint(expr=10*model.u + 9*model.v <= model.r)
model.labor_A = Constraint(expr=model.u + model.v <= 80)   # example limit
model.labor_B = Constraint(expr=2*model.u + model.v <= 100) # example limit
```

**Step 8: Solve the Model**
```python
solver = SolverFactory('highs')
result = solver.solve(model)
```

**Step 9: Report the Solution**
```python
print(f"Units of U: {value(model.u)}")
print(f"Units of V: {value(model.v)}")
print(f"Raw material ordered: {value(model.r)} grams")
print(f"Maximum profit: ${value(model.profit)}")
```

### What a "Data-Driven" Model Means

Instead of hardcoding numbers like `80` for labor hours, a data-driven model reads them from a dictionary or file:
```python
data = {'labor_A': 80, 'labor_B': 100, 'raw_material_cost': 10}
```
This makes the model reusable — change the data, run again, get new results without rewriting code.

---

## 4. Task: Linear Duality – Building Microchips Optimality Certificate

### Background Concepts

**How do you know a solution is truly the best?**  
When a solver returns an answer, it also provides an **optimality certificate** — a mathematical proof that no better solution exists. This comes from a concept called **duality**.

**What is Duality?**  
Every optimization problem (called the **primal** problem) has a matching **dual** problem. The dual problem finds the tightest possible *bound* on the objective value.

- If the primal is a maximization problem, the dual gives an **upper bound**
- When the optimal values of both primal and dual are equal, you have **strong duality** — this equality is the optimality certificate

Think of it like a negotiation: the primal asks "what's the most I can make?", and the dual asks "what's the minimum anyone could prove I can make?" When they agree, the answer is proven optimal.

### The Building Microchips Example

Imagine a factory producing microchips using two machines (Machine 1 and Machine 2), with limited hours per week.

**Primal problem (maximize profit):**
```
Maximize:   5·x₁ + 4·x₂
Subject to:
  6·x₁ + 4·x₂ ≤ 24   (Machine 1 capacity)
  x₁ + 2·x₂ ≤ 6      (Machine 2 capacity)
  x₁, x₂ ≥ 0
```
Here `x₁` and `x₂` are units of two chip types produced.

### How to Construct the Dual

**Step 1:** For each constraint in the primal, introduce a new variable (called a **dual variable** or **multiplier**):
- `y₁` for the Machine 1 constraint
- `y₂` for the Machine 2 constraint

**Step 2:** The dual objective is the right-hand side of the primal constraints, weighted by dual variables:
```
Minimize:  24·y₁ + 6·y₂
```

**Step 3:** For each primal variable, write a dual constraint using the primal's objective coefficients:
```
6·y₁ + y₂ ≥ 5    (for x₁)
4·y₁ + 2·y₂ ≥ 4  (for x₂)
y₁, y₂ ≥ 0
```

**Step 4:** Solve both. If both optimal values are equal, the solution is certified optimal.

### Intuition

The dual variables `y₁` and `y₂` represent the **shadow prices** — how much more profit you'd make if you had one extra hour of Machine 1 or Machine 2. They're the economic value of each resource.

---

## 5. Task: Finding the Dual of a General LO Problem

### The Task

Given a general LO problem with three variables and mixed constraint types (≤, ≥, =), derive its dual.

### Rules for Building the Dual

The dual construction follows a systematic table of rules:

| Primal (Minimization) | Dual (Maximization) |
|---|---|
| Constraint `≥` | Dual variable `≥ 0` |
| Constraint `=` | Dual variable unrestricted |
| Constraint `≤` | Dual variable `≤ 0` |
| Variable `≥ 0` | Dual constraint `≤` |
| Variable `≤ 0` | Dual constraint `≥` |
| Variable unrestricted | Dual constraint `=` |

**Key rule:** If the primal has `n` variables and `m` constraints, the dual has `m` variables and `n` constraints. The dimensions flip!

### Example

**Primal:**
```
Minimize:   2·x₁ + 3·x₂ - x₃
Subject to:
  x₁ + x₂ + x₃ ≥ 4    → dual variable y₁ ≥ 0
  2·x₁ - x₂ = 1        → dual variable y₂ unrestricted
  x₁ ≥ 0, x₂ ≥ 0, x₃ unrestricted
```

**Dual:**
```
Maximize:   4·y₁ + y₂
Subject to:
  y₁ + 2·y₂ ≤ 2     (for x₁ ≥ 0)
  y₁ - y₂ ≤ 3       (for x₂ ≥ 0)
  y₁ = -1            (for x₃ unrestricted)
  y₁ ≥ 0
```

---

## 6. Task: Sales Package Optimization (Phones Example)

### The Problem

A store manager has leftover stock to sell:
- **8 phones**
- **4 hands-free kits**
- **19 prepaid cards**

She can create two types of packages:

| Package | Contains | Profit |
|---|---|---|
| Package A | 1 phone + 2 prepaid cards | $7 |
| Package B | 1 phone + 1 hands-free kit + 3 prepaid cards | $9 |

**Part (a):** How many of each package maximizes total profit?  
**Part (b):** A supermarket wants to buy the *whole stock* (all phones, kits, and cards individually). What minimum unit prices must the manager charge so she earns at least as much as with the packages?  
**Part (c):** If the supermarket offers $65 or $62 for everything, should she accept?

### Part (a) – Solving the Primal

**Decision Variables:**
- `a` = number of Package A bundles
- `b` = number of Package B bundles

**Objective:**
```
Maximize:  7·a + 9·b
```

**Constraints (stock limits):**
```
a + b ≤ 8          (phones)
b ≤ 4              (hands-free kits)
2·a + 3·b ≤ 19    (prepaid cards)
a, b ≥ 0
```

**Solve:** Using a solver or graphically:
- Optimal: `a = 5, b = 3` → Profit = $7×5 + $9×3 = **$62**

### Part (b) – The Dual (Pricing Problem)

Now she wants unit prices for each product:
- `p` = price per phone
- `q` = price per hands-free kit
- `s` = price per prepaid card

The supermarket must pay at least as much as each package earns:
```
p + 2s ≥ 7     (Package A must be worth at least $7)
p + q + 3s ≥ 9 (Package B must be worth at least $9)
p, q, s ≥ 0
```

Minimize total payment:
```
Minimize:  8·p + 4·q + 19·s
```

This is exactly the **dual** of Part (a)!

### Part (c) – Interpretation

- The primal optimal = **$62**
- If the supermarket offers **$65**: Accept! It's more than the package profit of $62.
- If the supermarket offers **$62**: Both options yield the same profit. The manager is indifferent — she can choose based on convenience.

---

## 7. Task: BigFarm – Minimize Crop Growing Cost

### The Problem

BigFarm has **two farms** that each have **100 acres** available.  
They grow **wheat** and **corn**, but yields and costs differ per farm.

| | Farm 1 | Farm 2 |
|---|---|---|
| Wheat yield (bushels/acre) | Higher | Lower |
| Wheat cost ($/acre) | Different | Different |
| Corn yield (bushels/acre) | Different | Different |
| Corn cost ($/acre) | Different | Different |

**Demand to meet:**
- At least **11,000 bushels** of wheat
- At least **7,000 bushels** of corn

**Goal:** Find the planting plan that meets demand at **minimum cost**.

### How to Solve It

**Decision Variables:**
- `w₁` = acres of wheat at Farm 1
- `w₂` = acres of wheat at Farm 2
- `c₁` = acres of corn at Farm 1
- `c₂` = acres of corn at Farm 2

**Objective (minimize total cost):**
```
Minimize: cost_w1·w₁ + cost_w2·w₂ + cost_c1·c₁ + cost_c2·c₂
```

**Constraints:**
```
w₁ + c₁ ≤ 100          (Farm 1 acreage limit)
w₂ + c₂ ≤ 100          (Farm 2 acreage limit)
yield_w1·w₁ + yield_w2·w₂ ≥ 11000  (wheat demand)
yield_c1·c₁ + yield_c2·c₂ ≥ 7000   (corn demand)
w₁, w₂, c₁, c₂ ≥ 0
```

A solver finds the optimal split: how many acres of each crop to plant on each farm.

---

## 8. Task: Mixed-Integer Optimization – Building Microchips with Waste

### Background Concepts

**What is a Mixed-Integer Linear Optimization (MILO) problem?**  
Sometimes decision variables must be **whole numbers (integers)** — you can't hire 2.7 workers or produce 5.3 machines. When some variables must be integers and others can be fractional, it's called a Mixed-Integer problem.

MILO is harder than pure LO because there are far more combinations to check.

### The Problem

The microchip factory produces two types of chips. During production, some raw silicon wafers are wasted — and waste can only happen in whole batches. So the number of wasted batches must be an **integer**.

The standard LO model is adjusted:
```
Minimize: cost
Subject to:
  [same resource constraints as before]
  x₁, x₂ ≥ 0      (can be fractional — units of chips)
  w ∈ {0, 1, 2, …} (waste batches must be whole numbers)
```

### Why This is Harder

In pure LO, the simplex method finds the optimum by walking along edges of a shape. With integers, many of those edge points aren't valid (they're fractional). You have to search among only the valid integer points — and there can be over a million combinations even for small problems.

---

## 9. Task: Knapsack Problem (Resource Allocation)

### Background Concepts

The **Knapsack Problem** is one of the most famous problems in computer science. It models any situation where you have a **limited budget/capacity** and need to choose the **best combination** of items.

### The Problem

You have a knapsack (bag) that can carry at most `W` kg. You have `n` items, each with:
- A **weight** `wᵢ`
- A **value** `vᵢ`

Which items do you pack to maximize total value without exceeding the weight limit?

### Mathematical Formulation

**Decision Variables:** Binary variables `xᵢ ∈ {0, 1}`:
- `xᵢ = 1` means item `i` is packed
- `xᵢ = 0` means it's left out

**Objective:**
```
Maximize:  Σ vᵢ·xᵢ
```

**Constraint:**
```
Σ wᵢ·xᵢ ≤ W
xᵢ ∈ {0, 1} for all i
```

### How to Solve It

This is a MILO problem. Modern solvers handle it well up to thousands of items. The key insight: binary variables (`0` or `1`) are a special case of integers, making this a **Binary Integer Program (BIP)**.

### Real-World Uses

- Staff scheduling (assign/don't assign workers to shifts)
- Project portfolio selection (do/don't fund projects)
- Cargo loading (include/exclude items in a shipment)

---

## 10. Task: MILO Modeling Techniques (Logical Constraints)

### Background Concepts

Real-world problems often have **logical rules** like:
- "If machine A is on, then machine B must also be on"
- "Either we ship via Route 1 OR Route 2, not both"
- "There's a fixed startup cost when production exceeds zero"

These can't be expressed with simple linear inequalities — we need **binary (0/1) variables** and special tricks.

### Technique 1: Variables Taking Discontinuous Values

**Problem:** A variable `x` must be either 0, or between 5 and 10 — not between 1 and 4.

**Solution:** Use a binary variable `δ`:
```
x = δ · y,  where 5 ≤ y ≤ 10,  δ ∈ {0,1}
```
If `δ=0`: x=0. If `δ=1`: x=y (which is between 5 and 10).

### Technique 2: Switching a Constraint On or Off

**Problem:** A power plant's output `p` must be ≥ a minimum level `p_min` — **but only when it's switched on**. When off, output = 0.

**Solution:** Use binary variable `δ` (1=on, 0=off) and a large constant `M`:
```
p ≥ p_min · δ
p ≤ M · δ
δ ∈ {0, 1}
```
When `δ=0`: both constraints give `p ≥ 0` and `p ≤ 0`, so p=0 (off).  
When `δ=1`: `p ≥ p_min` and `p ≤ M` (the maximum capacity).

### Technique 3: Fixed Cost (Cost with a Setup Fee)

**Problem:** Producing any quantity of a product costs a fixed setup fee `f`, plus a variable cost per unit.

**Objective term:**
```
f·δ + c·x
```
Where `δ=1` if production is positive, `δ=0` otherwise.

Constraint linking them:
```
x ≤ M·δ
```
This forces `δ=1` whenever `x > 0`.

### Technique 4: Either-Or Constraints

**Problem:** Either constraint A holds, OR constraint B holds (or both).

Represent as:
```
A_expression ≤ M·δ
B_expression ≤ M·(1 - δ)
δ ∈ {0, 1}
```
When `δ=1`: constraint B is enforced; A is relaxed (M is large enough to make it inactive).  
When `δ=0`: constraint A is enforced; B is relaxed.

### Technique 5: If-Then Constraints

**Problem:** "If machine X is used, then it must finish before machine Y starts."

Using binary variables for each job assignment, you can write:
```
start_Y ≥ end_X - M·(1 - δ)
```
When `δ=1` (X is used): this becomes `start_Y ≥ end_X` (Y starts after X finishes).  
When `δ=0`: the right side is very negative, so the constraint is automatically satisfied.

### The Big-M Method and Its Limitations

All these techniques use a large constant `M`. The value of `M` must be:
- Big enough to make the constraint inactive when needed
- Not so large that it causes numerical instability in the solver

Choosing `M` requires knowledge of your problem's data.

### Disjunctive Programming – A Cleaner Alternative

Instead of big-M tricks, **disjunctive programming** directly expresses "OR" logic:
```
Either: [constraint set A]
Or:     [constraint set B]
```
Pyomo supports this natively with `Disjunct` and `Disjunction` objects. It's more readable and can sometimes lead to faster solving.

---

## 11. Task: Branch and Bound – Solving a MILO Problem

### Background Concepts

**Branch and Bound** is the most widely used algorithm for solving integer programs. Here's the intuition:

Imagine you're searching for a treasure in a house with many rooms. You don't check every corner — instead, you estimate which rooms are most promising and search those first. If a room's best possible treasure is less than what you've already found elsewhere, you skip it entirely.

### The Algorithm

**Step 1 – Solve the Linear Relaxation**  
Forget the integer requirements temporarily. Solve the easier LP version. This gives the **upper bound** (best case scenario, possibly fractional).

Example: Optimal LP solution is `x₁ = 3.7, x₂ = 2.1, objective = 45.8`

**Step 2 – Branch**  
Pick one fractional variable, say `x₁ = 3.7`. Create two subproblems:
- Branch LEFT: Add constraint `x₁ ≤ 3`
- Branch RIGHT: Add constraint `x₁ ≥ 4`

**Step 3 – Solve Each Subproblem**  
Solve the LP relaxation of each subproblem.

**Step 4 – Prune (Discard) Bad Branches**  
Discard a branch if:
- It's **infeasible** (no valid solution exists)
- Its best possible value is **worse than the best integer solution found so far**
- The LP solution is **already integer** (no need to branch further)

**Step 5 – Repeat**  
Keep branching until all branches are pruned or solved.

**Step 6 – Best Integer Solution = Global Optimum**

### Visual Example

```
Root: LP relaxation → x₁=3.7, x₂=2.1, obj=45.8
       /                          \
x₁≤3                           x₁≥4
LP: x₁=3, x₂=2.3, obj=44.1    LP: x₁=4, x₂=1.8, obj=43.6
       /         \
  x₂≤2         x₂≥3
obj=43 ✓INT   infeasible ✗
```

Best integer solution: `x₁=3, x₂=2, obj=43` — proven optimal.

---

## 12. Task: Network Optimization – Dinner Seating Arrangement

### Background Concepts

**What is a Network?**  
A network is a set of **nodes** (points) connected by **edges** (lines/arrows). Networks model relationships: roads between cities, friendships between people, pipelines between tanks.

In optimization, networks let us visualize and solve problems more efficiently than writing out all constraints manually.

### The Dinner Seating Problem

You're organizing a dinner. You have guests and tables. Some guests prefer (or cannot be seated with) certain others. Model this as a network:
- **Nodes** = guests
- **Edges** = compatibility (or incompatibility) between guests

**Goal:** Assign guests to tables such that preferences are respected and tables aren't overcrowded.

### Why Networks Are Special

When an optimization problem has a **network structure**, the constraint matrix has a special property called **Total Unimodularity (TU)**.

**What is TU?**  
A matrix is totally unimodular if every square submatrix has a determinant of 0, +1, or -1.

**Why does TU matter?**  
If the constraint matrix is TU, then solving the LP relaxation (ignoring integer constraints) will automatically give **integer solutions**. This means you can solve the much harder MILO problem just as easily as a simple LP!

---

## 13. Task: Transport Logistics as a Network Problem

### The Problem

Goods need to be shipped from **supply points** (warehouses, factories) to **demand points** (customers, stores). Each shipping route has a cost per unit.

**Goal:** Determine the flow on each route to minimize total cost while meeting all supply and demand requirements.

### Network Representation

- **Nodes with positive numbers** = supply locations (they have goods to send)
- **Nodes with negative numbers** = demand locations (they need goods)
- **Edges/arcs** = shipping routes, each with a cost and capacity

### The Minimum-Cost Flow Problem

```
Minimize:  Σ cost(arc) · flow(arc)
Subject to:
  Flow conservation at each node:
    [inflow] - [outflow] = [supply/demand at node]
  Lower bound ≤ flow ≤ upper bound for each arc
```

**Flow conservation** means: what goes in equals what goes out (plus or minus local supply/demand). Think of it like water in pipes — nothing disappears.

---

## 14. Task: Shortest Path – Dijkstra's Algorithm

### Background Concepts

**Shortest Path Problem:** Given a network of nodes and edges with distances/costs, find the path from a start node to an end node with the **minimum total cost**.

Applications: GPS navigation, internet routing, logistics.

### Dijkstra's Algorithm – Step by Step

**Setup:** Label every node with its current best distance from the start (initially all infinity except the start, which is 0).

**Algorithm:**
1. Start at the source node (distance = 0).
2. For each neighbor, update their distance if going through the current node is shorter.
3. Mark the current node as "visited" (finalized).
4. Move to the unvisited node with the smallest current distance.
5. Repeat until you reach the destination.

### Example

Find shortest path from **a** to **c**:

```
Network:
  a --4-- b
  |       |
  2       3
  |       |
  d --1-- c
```

**Step 1:** Start at `a`. Distance to `a = 0`.  
**Step 2:** Neighbors of `a`: `b` (distance 4), `d` (distance 2).  
**Step 3:** Go to `d` (distance 2, smallest). From `d`, reach `c` (2+1=3).  
**Step 4:** Go to `c` (distance 3). Done!

Shortest path: `a → d → c`, total cost = **3**  
(vs. `a → b → c` = 4+3 = 7)

---

## 15. Task: Transportation Problem – Gasoline Distribution

### The Problem

Several refineries (supply) must ship gasoline to several gas stations (demand). Each route has a cost per liter. How do you minimize total shipping cost?

### Formulation

**Decision Variables:**
- `x_{ij}` = amount shipped from refinery `i` to station `j`

**Objective:**
```
Minimize:  Σᵢ Σⱼ cost_{ij} · x_{ij}
```

**Constraints:**
```
Σⱼ x_{ij} ≤ supply_i    for each refinery i    (can't ship more than available)
Σᵢ x_{ij} ≥ demand_j    for each station j      (must meet demand)
x_{ij} ≥ 0
```

### Why It's a Network Problem

This is a special case of **Minimum-Cost Flow**. The constraint matrix is TU → the LP relaxation gives integer solutions automatically. This is great because integers are required (you can't ship fractional liters of gasoline in practice).

---

## 16. Task: Rebalancing a Bike-Sharing System

### The Problem

A city has a bike-sharing system with stations. Some stations have **too many** bikes (surplus), others have **too few** (deficit). A truck must **rebalance** — move bikes from surplus to deficit stations — at minimum cost.

### Formulation

This is another **Minimum-Cost Flow** problem:
- **Nodes** = bike stations
- **Supply** at nodes = number of excess bikes
- **Demand** at nodes = number of missing bikes
- **Edges** = truck routes between stations, with cost per bike moved

**Goal:** Move bikes at minimum total transportation cost.

---

## 17. Task: Max-Weight Bipartite Matching

### Background Concepts

**Bipartite graph:** A graph where nodes are split into two groups, and edges only connect nodes from different groups (never within the same group).

**Matching:** A set of edges where each node appears at most once.

**Max-Weight Matching:** Find a matching that maximizes the total weight of chosen edges.

### Example: Job Assignment

- Group A: 3 workers
- Group B: 3 jobs
- Edge weight = how well suited worker `i` is for job `j`

**Goal:** Assign each worker to a job (or none) to maximize total suitability.

### Formulation

**Decision Variables:** Binary `x_{ij}` (1 = assign worker i to job j)

**Objective:**
```
Maximize:  Σᵢ Σⱼ weight_{ij} · x_{ij}
```

**Constraints:**
```
Σⱼ x_{ij} ≤ 1   for each worker i   (each worker gets at most 1 job)
Σᵢ x_{ij} ≤ 1   for each job j      (each job assigned to at most 1 worker)
x_{ij} ∈ {0, 1}
```

**Good news:** This constraint matrix is TU, so the LP relaxation gives integer (0/1) solutions automatically!

---

## 18. Task: Maximum Matching and Max-Flow / Min-Cut

### Maximum Matching

Find the **largest possible matching** in a graph (not necessarily bipartite) — the greatest number of node pairs you can connect using non-overlapping edges.

Used in: scheduling, pairing, resource allocation.

### Max-Flow Problem

**Given:** A network with a source `s` (where flow originates) and a sink `t` (where flow ends). Each edge has a **capacity** (maximum flow it can carry).

**Goal:** Find the maximum total flow from `s` to `t`.

### Min-Cut Theorem

A **cut** is a set of edges whose removal disconnects the source from the sink.

**The Max-Flow Min-Cut Theorem** (one of the most beautiful results in combinatorics):

> **Maximum flow = Minimum cut capacity**

This means: the bottleneck in a network (the weakest link) equals the maximum possible throughput.

**Practical use:** Find the minimum number of roads you'd need to block to completely stop traffic from one city to another.

---

## 19. Task: Traveling Salesman Problem (TSP)

### The Problem

A salesman must visit `n` cities **exactly once** and return home. What route minimizes total distance?

### Why It's Hard

With `n` cities, there are `(n-1)!/2` possible routes. For 20 cities: over 60 trillion routes!

TSP is **NP-hard** — no known algorithm solves it efficiently for large `n`. This doesn't mean it's unsolvable, but current solvers use clever approximations and branch-and-bound techniques.

### MILO Formulation

**Decision Variables:** Binary `x_{ij} = 1` if the route goes from city `i` to city `j`.

**Objective:**
```
Minimize:  Σᵢ Σⱼ dist_{ij} · x_{ij}
```

**Constraints:**
```
Σⱼ x_{ij} = 1   for each city i   (leave each city exactly once)
Σᵢ x_{ij} = 1   for each city j   (arrive at each city exactly once)
x_{ij} ∈ {0,1}
+ subtour elimination constraints  (prevents mini-loops that don't visit all cities)
```

Subtour elimination is the hard part — there are exponentially many such constraints.

---

## 20. Task: Graph Coloring

### The Problem

Color the nodes of a graph so that **no two adjacent nodes share the same color**, using as few colors as possible.

**Real applications:** Scheduling (if two tasks conflict, give them different time slots), frequency assignment in networks, register allocation in compilers.

### MILO Formulation

**Decision Variables:**
- Binary `x_{iv} = 1` if node `i` is assigned color `v`
- Binary `y_v = 1` if color `v` is used at all

**Objective:**
```
Minimize:  Σᵥ y_v    (minimize number of colors)
```

**Constraints:**
```
Σᵥ x_{iv} = 1                   (each node gets exactly 1 color)
x_{iv} + x_{jv} ≤ y_v           (adjacent nodes i,j can't share color v)
x_{iv} ∈ {0,1}, y_v ∈ {0,1}
```

---

## 21. Task: Arbitrage Search in Cryptocurrency Markets

### Background Concepts

**Arbitrage** means exploiting price differences across markets to make a profit with no risk. In currency/crypto markets: if you convert USD → EUR → GBP → USD and end up with more USD than you started, that's arbitrage.

### Network Formulation

- **Nodes** = currencies (USD, EUR, BTC, ETH, etc.)
- **Edges** = exchange rate from currency A to currency B
- **Edge weight** = log of the exchange rate (using logarithms turns multiplication into addition)

**Why logarithms?** If you multiply exchange rates `r₁ × r₂ × r₃`, taking logs converts this to `log(r₁) + log(r₂) + log(r₃)`. This lets you use shortest-path algorithms!

### Finding Arbitrage

Arbitrage exists if there's a **negative-weight cycle** in the graph (using negated log exchange rates).

- Use the **Bellman-Ford algorithm** (shortest path that handles negative weights) to detect such cycles.
- If a negative cycle is found → arbitrage opportunity exists!

---

## 22. Task: Convex Optimization – Milk Pooling and Blending

### Background Concepts

**What is Convex Optimization?**

A function is **convex** if the line segment between any two points on the function lies *above or on* the function. Think of a bowl shape — the bottom is the minimum, and there are no other "fake" dips.

**Why does convexity matter?**  
For convex problems, **every local minimum is also the global minimum**. This means any optimization solver will find the true best answer, not get stuck in a false valley.

### The Problem

A dairy company has several milk sources with different fat content percentages. They pool (mix) milk and blend it to meet specific quality requirements for different products.

The blending ratios lead to **nonlinear** equations (multiplying percentages by quantities). However, if the problem is **convex**, we can still solve it efficiently.

### Multiple Local Optima Problem

For non-convex problems, the optimization landscape looks like a mountain range with many valleys. A solver might find a local valley (locally optimal) but miss the deepest valley (globally optimal).

Convexity guarantees there's only ONE valley — so any solver finds the global minimum.

---

## 23. Task: Center of Mass of a Glass of Water

### The Problem

Given a glass of water with varying density (e.g., sugar dissolved unevenly), find the **center of mass** — the point where the glass would perfectly balance.

### Why It's an Optimization Problem

The center of mass minimizes the **sum of weighted squared distances** from all mass points. This is a **quadratic** (nonlinear) objective, but it's convex (bowl-shaped), so it's easy to solve.

**Formula:**
```
Center of mass x̄ = Σ mᵢ·xᵢ / Σ mᵢ
```

This is the solution to:
```
Minimize: Σ mᵢ · (x - xᵢ)²
```

---

## 24. Task: Ordinary Least Squares Regression

### Background Concepts

**Regression** is fitting a line (or curve) to data points so the line best represents the trend.

**Least Squares:** Minimize the sum of squared vertical distances from each data point to the fitted line.

### The Problem

Given `n` data points `(x₁, y₁), ..., (xₙ, yₙ)`, find coefficients `β₀` and `β₁` such that:

```
ŷᵢ = β₀ + β₁·xᵢ   (predicted value)
```

minimizes:

```
Minimize:  Σᵢ (yᵢ - ŷᵢ)²  =  Σᵢ (yᵢ - β₀ - β₁·xᵢ)²
```

### Why It's Convex

The objective is a **sum of squares** — and squares are convex functions. Sums of convex functions are convex. Therefore, this is a convex optimization problem with a unique global minimum.

**Closed-form solution (no solver needed):**
```
β = (XᵀX)⁻¹ Xᵀy
```
Where X is the matrix of input data.

---

## 25. Task: Markowitz Portfolio Optimization

### Background Concepts

When investing, you want **high return** but also **low risk**. These two goals conflict. The Markowitz model finds the optimal balance.

- **Return** of a portfolio = weighted average of individual asset returns
- **Risk** = variance of the portfolio return (how much it fluctuates)

### The Problem

You have `n` assets. You want to allocate your budget (weights summing to 1) to:

```
Minimize:  Risk (portfolio variance)
Subject to:
  Expected return ≥ target return r*
  Σ wᵢ = 1     (weights sum to 100%)
  wᵢ ≥ 0       (no short-selling constraint)
```

### Mathematical Form

**Risk (variance):**
```
Minimize:  wᵀ Σ w
```
Where `w` = weight vector, `Σ` = covariance matrix of asset returns.

**This is a Quadratic Program (QP)** — a convex problem (because `Σ` is positive semi-definite). It can be solved efficiently.

### The Efficient Frontier

By varying the target return `r*`, you trace out the **efficient frontier** — the set of portfolios offering the best possible return for each level of risk.

---

## 26. Task: KKT Conditions and Duality for Convex Optimization

### Background Concepts

**Lagrangian Relaxation:** A technique to "relax" constraints by moving them into the objective function with a penalty.

For a constrained problem:
```
Minimize:  f(x)
Subject to:  g(x) ≤ 0
```

The **Lagrangian** is:
```
L(x, λ) = f(x) + λ · g(x)
```
Where `λ ≥ 0` is the **Lagrange multiplier** (the dual variable).

### KKT Conditions

The **Karush-Kuhn-Tucker (KKT) conditions** are necessary (and sufficient for convex problems) for optimality. A solution `x*` is optimal if and only if:

1. **Stationarity:** ∇f(x*) + Σ λᵢ · ∇gᵢ(x*) = 0  
   *(Gradient of Lagrangian = 0)*

2. **Primal Feasibility:** gᵢ(x*) ≤ 0 for all i  
   *(All constraints satisfied)*

3. **Dual Feasibility:** λᵢ ≥ 0 for all i  
   *(Dual variables are non-negative)*

4. **Complementary Slackness:** λᵢ · gᵢ(x*) = 0 for all i  
   *(Either constraint is tight, or its multiplier is zero)*

### Intuition for Complementary Slackness

If a constraint is not tight (you have slack), it's not limiting you — so its "shadow price" (multiplier) should be zero. If a constraint IS tight (binding), it might have a positive price.

### Why KKT Matters

Every numerical solver for nonlinear optimization is, under the hood, solving the KKT conditions. Understanding KKT tells you what the solver is actually doing.

---

## 27. Task: Support Vector Machines (SVM) for Binary Classification

### Background Concepts

**Classification** in machine learning means: given data points with labels (e.g., spam/not-spam), learn a rule to classify new points.

**SVM** finds the **best separating hyperplane** (line in 2D, plane in 3D) between two classes.

### The Problem

Given labeled training points `(xᵢ, yᵢ)` where `yᵢ ∈ {+1, -1}`:

Find a hyperplane `w·x + b = 0` that:
- Correctly classifies all points
- **Maximizes the margin** (the gap between the two classes)

### Formulation

```
Minimize:   ½ · ||w||²        (maximize margin = minimize norm of w)
Subject to: yᵢ · (w·xᵢ + b) ≥ 1   for all i
```

**This is a Quadratic Program (QP)** — convex, solvable efficiently.

### Soft Margin (Real-World Version)

Data is rarely perfectly separable. The **soft-margin SVM** allows some misclassification:

```
Minimize:   ½ · ||w||² + C · Σ ξᵢ
Subject to: yᵢ · (w·xᵢ + b) ≥ 1 - ξᵢ
            ξᵢ ≥ 0
```

`C` controls the trade-off between margin size and misclassification penalty.

### Dual Formulation

The SVM has a natural dual that leads to the **kernel trick** — allowing SVMs to work in high-dimensional spaces without explicitly computing them.

---

## 28. Task: Economic Order Quantity – Conic Optimization

### Background Concepts

**What is Conic Optimization?**

Conic optimization is a subclass of convex optimization where the feasible set is defined by **cones** — mathematical structures that generalize linear constraints. Three common cones:

- **Linear cone** (LP): `x ≥ 0`
- **Second-order cone (SOCP)**: `||u|| ≤ t`
- **Semidefinite cone (SDP)**: matrix `X` is positive semidefinite

**Why Conic?**  
Conic problems have systematic dual derivations (just like LP), making optimality certificates easier to obtain than for general convex problems.

### The Economic Order Quantity (EOQ) Problem

**Scenario:** A store sells `D` units per year of a product. Each time they order more stock, there's a fixed ordering cost `K`. Holding inventory costs `h` per unit per year.

**Trade-off:**
- Order **less often** → less ordering cost, but more inventory holding cost
- Order **more often** → less inventory, but more ordering cost

**Goal:** Find the optimal order quantity `Q` to minimize total annual cost.

**Total cost:**
```
C(Q) = K · (D/Q) + h · (Q/2)
```

- `K · D/Q` = annual ordering cost (number of orders × cost per order)
- `h · Q/2` = annual holding cost (average inventory × cost per unit)

### Solving It

This is a nonlinear (but convex) problem. Taking the derivative and setting it to zero:

```
dC/dQ = -K·D/Q² + h/2 = 0
→ Q* = √(2·K·D/h)
```

This is the famous **EOQ formula**. As a conic optimization problem (second-order cone form), it can be solved by conic solvers and its dual gives shadow prices on the inventory and demand constraints.

---

## Summary Table: All Tasks

| # | Task | Type | Key Concept |
|---|---|---|---|
| 2 | Production Planning | Linear Optimization | Decision variables, constraints, objective |
| 3 | Pyomo Implementation | Software | Modeling in Python, using solvers |
| 4 | Microchips Optimality Certificate | Duality | Primal/dual, shadow prices |
| 5 | General Dual Derivation | Duality | Dual construction rules |
| 6 | Phone Packages | LP + Duality | Primal and dual interpretation |
| 7 | BigFarm Crops | LP (minimize) | Resource allocation across locations |
| 8 | Microchips with Waste | MILO | Integer variables |
| 9 | Knapsack | MILO (Binary) | 0/1 variables, NP-hard |
| 10 | Logical Constraints | MILO Modeling | Big-M, disjunctive programming |
| 11 | Branch and Bound | MILO Algorithm | Tree search, pruning |
| 12 | Dinner Seating | Network / TU | Graph structure, integer solutions for free |
| 13 | Transport Logistics | Network (Min-Cost Flow) | Flow conservation |
| 14 | Shortest Path (Dijkstra) | Network Algorithm | Greedy path finding |
| 15 | Gasoline Distribution | Transportation LP | Supply/demand matching |
| 16 | Bike Rebalancing | Transportation LP | Redistribution as flow |
| 17 | Job Assignment | Bipartite Matching | TU matrix, binary assignment |
| 18 | Max-Flow / Min-Cut | Network | Max-flow = min-cut theorem |
| 19 | Traveling Salesman | MILO (NP-hard) | Combinatorial explosion |
| 20 | Graph Coloring | MILO | Scheduling / conflict avoidance |
| 21 | Crypto Arbitrage | Network (Bellman-Ford) | Negative cycles, log-transform |
| 22 | Milk Blending | Nonlinear / Convex | Local vs global optima |
| 23 | Center of Mass | Convex (Quadratic) | Weighted least squares |
| 24 | Least Squares Regression | Convex (Quadratic) | Fitting lines to data |
| 25 | Portfolio Optimization | Convex (QP) | Risk-return trade-off |
| 26 | KKT Conditions | Convex Duality | Optimality conditions |
| 27 | Support Vector Machines | Convex (QP) | Classification, margin maximization |
| 28 | Economic Order Quantity | Conic Optimization | Second-order cone, EOQ formula |

---

## Key Terms Glossary

| Term | Plain-English Definition |
|---|---|
| **Decision Variable** | A number you get to choose (e.g., how many products to make) |
| **Objective Function** | The formula for what you're trying to maximize or minimize |
| **Constraint** | A rule or limit the solution must respect |
| **Feasible Set** | All solutions that satisfy the constraints |
| **Global Optimum** | The single best solution across all feasible solutions |
| **Linear Optimization (LO)** | Optimization where everything is proportional (linear) |
| **MILO** | Optimization where some variables must be integers |
| **Binary Variable** | A variable that can only be 0 or 1 (yes/no decisions) |
| **Solver** | Software that finds the optimal solution automatically |
| **Dual Problem** | A mirror problem whose optimal value certifies the primal's optimality |
| **Shadow Price** | How much the objective would improve with one more unit of a resource |
| **Network / Graph** | A set of nodes (points) connected by edges (lines) |
| **Totally Unimodular (TU)** | A matrix property that guarantees integer LP solutions |
| **Convex Function** | A bowl-shaped function with only one minimum |
| **KKT Conditions** | Mathematical rules that characterize the optimal point of a convex problem |
| **Conic Optimization** | Convex optimization over cone-shaped feasible sets |
| **Big-M Method** | A technique using a large constant M to model logical on/off constraints |
| **Branch and Bound** | Algorithm for solving integer programs by tree search |
| **NP-hard** | A class of problems for which no fast general algorithm is known |
| **Dijkstra's Algorithm** | A greedy algorithm for finding shortest paths in a graph |
| **Arbitrage** | Profiting from price differences across markets |
| **SVM** | A machine learning model that finds the best boundary between two classes |
| **EOQ** | The optimal order quantity that minimizes total inventory costs |
