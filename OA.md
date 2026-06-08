# IT — Optimization & Algorithms: Complete Study Notes

> **Structure:** Weeks 2–14 are covered in full. Each week maps to one lab session. Pyomo code examples are included for every practical topic. Mathematical formulations are written in plain-text notation. Week 14 covers Conic Optimization from the PDF chapter (mislabeled "Week 13" in the source).

---

## Table of Contents

1. [Week 2 — Mathematical Optimization: A General Model](#-week-2-mathematical-optimization-a-general-model)
2. [Week 3 — Modeling and Solving Problems With Pyomo](#-week-3-modeling-and-solving-problems-with-pyomo)
3. [Week 4 — Linear Optimization: Formulation and Modeling Techniques](#-week-4-linear-optimization-formulation-and-modeling-techniques)
4. [Week 5 — Linear Duality and Solution Methods](#-week-5-linear-duality-and-solution-methods)
5. [Week 6 — Linear Optimization Examples](#-week-6-linear-optimization-examples)
6. [Week 7 — Mixed-Integer Linear Optimization: Formulation and Modeling Techniques](#-week-7-mixed-integer-linear-optimization-formulation-and-modeling-techniques)
7. [Week 8 — Mixed-Integer Linear Optimization: Solution Methods and Examples](#-week-8-mixed-integer-linear-optimization-solution-methods-and-examples)
8. [Week 9 — Network Optimization: Introduction and Totally Unimodular Matrices](#-week-9-network-optimization-introduction-and-totally-unimodular-matrices)
9. [Week 10 — Special Network Problems and Examples](#-week-10-special-network-problems-and-examples)
10. [Week 11 — Convex Optimization: Introduction, Modeling and Verification](#-week-11-convex-optimization-introduction-modeling-and-verification)
11. [Week 12 — Duality for Convex Optimization and Solution Methods](#-week-12-duality-for-convex-optimization-and-solution-methods)
12. [Week 13 — Convex Optimization Examples](#-week-13-convex-optimization-examples)
13. [Week 14 — Conic Optimization](#-week-14-conic-optimization)

---

## 📅 Week 2: Mathematical Optimization — A General Model

### What Is Optimization?

**Optimization** means finding the **best possible solution** to a problem — not just any solution, not a random one, but the one that is objectively best according to a defined criterion.

Typical IT/engineering examples:
- Finding the shortest path in a network
- Minimizing execution time of a program
- Allocating limited resources on a server
- Reducing cost while maintaining performance

### Why Mathematical Models?

A computer cannot work with natural language descriptions such as "reduce cost as much as possible." It requires:
- **Numbers** and **variables** to represent decisions
- **Equations** or **inequalities** to represent constraints
- A **mathematical expression** for the goal

Translating a real-world problem into this structure is called **mathematical modeling**.

> **Key principle:** Optimization is about **modeling first**, not algorithms first. A wrong model produces a wrong answer even with a perfect algorithm.

### The Three Components of Every Optimization Model

| Component | Question | Example |
|---|---|---|
| **Decision variables** | What can we choose/control? | Number of VMs to deploy |
| **Objective function** | What are we minimizing or maximizing? | Total profit |
| **Constraints** | What rules must the solution satisfy? | CPU ≤ 40 units |

**General form:**

```
minimize (or maximize)   f(x)
subject to               g_i(x) ≤ 0   (or ≥ 0, or = 0)
                         x ≥ 0
```

### Decision Variables

A **decision variable** represents a choice we are free to make. They are the unknowns the solver will determine.

**What is NOT a decision variable:** Any value that is fixed in advance (costs, capacities, time limits) — these are **parameters**.

**Three types:**
- **Continuous** — can take any real value (e.g., x = 3.7 hours)
- **Integer** — must be a whole number (e.g., x = 5 servers)
- **Binary** — can only be 0 or 1 (yes/no decisions)

### Objective Function

The **objective function** is a mathematical expression that measures solution quality. It must:
- Depend on the decision variables
- Be written as a mathematical expression
- Explicitly state whether we minimize or maximize

One optimization problem has exactly one objective function.

### Constraints

**Constraints** are mathematical inequalities or equations that limit which solutions are allowed.

Common forms:
- Upper bound: `x ≤ 10`
- Lower bound: `x ≥ 0` (non-negativity — appears in almost every model)
- Equality: `x = 5`

A solution that satisfies **all** constraints is called **feasible**. Violating even one constraint makes a solution **infeasible**.

### Feasible Region

The **feasible region** is the set of all solutions that satisfy all constraints simultaneously. Optimization is performed exclusively within this region — the optimal solution is always found on or inside it.

> For LP problems, the optimal solution always lies at a **corner point (vertex)** of the feasible region.

### Worked Example: Cloud Server Allocation

**Problem:** A physical server has 40 CPU units and 80 GB RAM. Two VM types can be deployed:

| Resource | VM Type A | VM Type B |
|---|---|---|
| CPU units needed | 2 | 4 |
| RAM needed (GB) | 4 | 8 |
| Profit (€) | 30 | 70 |

**Decision variables:** x1 = number of Type A VMs, x2 = number of Type B VMs

**Mathematical model:**

```
maximize    30x1 + 70x2
subject to  2x1 + 4x2 ≤ 40     (CPU constraint)
            4x1 + 8x2 ≤ 80     (RAM constraint)
            x1, x2 ≥ 0
```

**Note:** Both constraints simplify to the same inequality (the second is twice the first), so only one is binding. **Optimal: x1 = 0, x2 = 10, profit = €700.**

**Feasible solution check (x1=10, x2=5):** CPU: 2·10+4·5=40 ✔, RAM: 4·10+8·5=80 ✔ — feasible but not optimal.

**Infeasible solution (x1=20, x2=10):** CPU: 70 > 40 ✗ — not allowed.

---

## 📅 Week 3: Modeling and Solving Problems With Pyomo

### How to Get the Best Solution: Three Approaches

| Approach | Description | Tradeoff |
|---|---|---|
| A — Existing algorithm | Implement a known algorithm (e.g., Dijkstra, Simplex) | Fast if done correctly; requires knowing the right algorithm |
| B — Custom algorithm | Design a new method | Highly customizable; hard to justify optimality |
| **C — Use a solver** | Write the model; let specialized software solve it | **Most common; reliable; often guarantees global optimality** |

### Solvers and Optimality Guarantees

A **solver** is a professional optimization engine that takes a model (variables, objective, constraints) and returns an optimal solution with a certificate that no better feasible solution exists.

**Local vs. Global Optimality:**
- **Local optimum** — better than all nearby solutions; may not be globally best
- **Global optimum** — the best solution across the entire feasible region
- For linear and mixed-integer linear models, solvers typically guarantee **global optimality**

### The Pyomo Workflow

```
User writes model  →  Pyomo translates  →  Solver computes  →  Pyomo presents results
   (human form)         to solver format       solution             to user
```

- **User** = model designer
- **Pyomo** = translator / interface
- **Solver** = optimization engine

**Solver independence:** The same Pyomo model can be solved by different solvers without rewriting. Switch from HiGHS (free) to Gurobi (commercial) by changing one line.

### Pyomo Setup

```python
import sys
if 'google.colab' in sys.modules:
    %pip install pyomo > /dev/null 2>/dev/null
    %pip install highspy > /dev/null 2>/dev/null

solver = 'appsi_highs'
import pyomo.environ as pyo
SOLVER = pyo.SolverFactory(solver)
assert SOLVER.available(), f"Solver {solver} is not available."
```

### Step-by-Step Pyomo Model: Cloud Server Allocation

**Step 1 — Import Pyomo:**
```python
import pyomo.environ as pyo
```
`pyomo.environ` provides: `Var`, `Objective`, `Constraint`, `ConcreteModel`.

**Step 2 — Create the model:**
```python
model = pyo.ConcreteModel("Cloud Server Allocation")
```
- **`ConcreteModel`** — all data is known when the model is created (used in this course).
- **`AbstractModel`** — structure defined first, data provided later.

**Step 3 — Define decision variables:**
```python
model.x_A = pyo.Var(bounds=(0, None))   # Number of Type A VMs
model.x_B = pyo.Var(bounds=(0, None))   # Number of Type B VMs
```
`bounds=(0, None)` means minimum = 0, no upper limit. Equivalent to `domain=pyo.NonNegativeReals`.

**Step 4 — Define expressions (optional intermediate values):**
```python
model.profit = 30 * model.x_A + 70 * model.x_B
```

**Step 5 — Define objective (decorator style):**
```python
@model.Objective(sense=pyo.maximize)
def maximize_profit(m):
    return m.profit
```

**Step 6 — Define constraints:**
```python
@model.Constraint()
def cpu_limit(m):
    return 2*m.x_A + 4*m.x_B <= 40

@model.Constraint()
def ram_limit(m):
    return 4*m.x_A + 8*m.x_B <= 80
```

**Step 7 — Solve:**
```python
results = SOLVER.solve(model, tee=True)
```

**Result:** x_A = 0, x_B = 10, profit = €700.

### Inspecting the Model

| Method | Purpose |
|---|---|
| `model.display()` | Shows current component values (useful after solving) |
| `model.pprint()` | Shows model structure — variable domains, constraint forms |
| `pyo.value(model.x_A)` | Extracts the numeric value of a solved variable |

### Advanced Modeling Technique 1: Absolute Value Linearization

To include `|g(x)|` in a minimization objective:
1. Introduce auxiliary variable `y ≥ 0`
2. Add: `y ≥ g(x)` and `y ≥ -g(x)`
3. Minimize `y` directly (since `y = |g(x)|` at optimum)

### Advanced Modeling Technique 2: Max-Min (Minimax) Objective

**Goal:** Maximize the minimum profit across multiple scenarios (robust planning).

Instead of `max min_i {profit_i}` (which is nonlinear), introduce variable `z` and:
```
maximize    z
subject to  z ≤ profit_i   for each scenario i
```

```python
def BIM_maxmin(costs):
    m = pyo.ConcreteModel("BIM production planning with maxmin objective")
    m.x1 = pyo.Var(domain=pyo.NonNegativeReals)
    m.x2 = pyo.Var(domain=pyo.NonNegativeReals)
    m.z  = pyo.Var()
    m.profit = pyo.Objective(sense=pyo.maximize, expr=m.z)
    m.maxmin = pyo.ConstraintList()
    for c1, c2 in costs:
        m.maxmin.add(expr=m.z <= c1*m.x1 + c2*m.x2)
    m.silicon   = pyo.Constraint(expr=m.x1 <= 1000)
    m.germanium = pyo.Constraint(expr=m.x2 <= 1500)
    m.plastic   = pyo.Constraint(expr=m.x1 + m.x2 <= 1750)
    m.copper    = pyo.Constraint(expr=4*m.x1 + 2*m.x2 <= 4800)
    return m

costs = [(12, 9), (11, 10), (8, 11)]
BIM = BIM_maxmin(costs)
SOLVER.solve(BIM)
```

### Advanced Modeling Technique 3: Fractional Objective (Charnes-Cooper Transformation)

**Goal:** Maximize a ratio like `profit / cost` (a linear-fractional objective).

**Transformation:** Let `t = 1/cost(x)`, `y = x·t`. Then:
- Objective becomes linear: `maximize Σ c_i · y_i`
- Add normalization: `cost(y) = 1` (i.e., cost expressed in `y` equals 1)
- Recover original variables: `x = y/t`

---

## 📅 Week 4: Linear Optimization — Formulation and Modeling Techniques

### What Is Linear Optimization (LP)?

A **Linear Program (LP)** is an optimization problem where:
- The objective function is **linear** in the decision variables
- All constraints are **linear** equalities or inequalities

LP is the most widely applied class of optimization. Every linear function is both convex and concave, which makes LP efficiently solvable.

### The BIM Microchip Production Problem

BIM manufactures two microchips:
- x1 = silicon-based chips
- x2 = germanium-based chips

| Constraint | x1 | x2 | Limit |
|---|---|---|---|
| Silicon wafers | 1 | 0 | 1000 |
| Germanium wafers | 0 | 1 | 1500 |
| Plastic units | 1 | 1 | 1750 |
| Copper wires | 4 | 2 | 4800 |

**Model:**
```
maximize    12x1 + 9x2
subject to  x1        ≤ 1000    (silicon)
            x2        ≤ 1500    (germanium)
            x1 + x2   ≤ 1750    (plastic)
            4x1 + 2x2 ≤ 4800   (copper)
            x1, x2    ≥ 0
```

**Corner point evaluation (find max of 12x1+9x2 at each vertex):**

| Vertex | x1 | x2 | Profit |
|---|---|---|---|
| (0, 0) | 0 | 0 | 0 |
| (0, 1500) | 0 | 1500 | 13,500 |
| (250, 1500) | 250 | 1500 | 16,500 |
| **(650, 1100)** | **650** | **1100** | **17,000 ← optimal** |
| (1000, 400) | 1000 | 400 | 15,600 |
| (1000, 0) | 1000 | 0 | 12,000 |

### Pyomo BIM Implementation

```python
m = pyo.ConcreteModel("BIM production planning")
m.x1 = pyo.Var(domain=pyo.NonNegativeReals)
m.x2 = pyo.Var(domain=pyo.NonNegativeReals)
m.profit     = pyo.Objective(expr=12*m.x1 + 9*m.x2, sense=pyo.maximize)
m.silicon    = pyo.Constraint(expr=m.x1 <= 1000)
m.germanium  = pyo.Constraint(expr=m.x2 <= 1500)
m.plastic    = pyo.Constraint(expr=m.x1 + m.x2 <= 1750)
m.copper     = pyo.Constraint(expr=4*m.x1 + 2*m.x2 <= 4800)
SOLVER.solve(m)
```

### Standard and Canonical LP Forms

**Standard form (maximization):**
```
maximize    c^T x
subject to  Ax ≤ b
            x ≥ 0
```

**Canonical form (minimization):**
```
minimize    c^T x
subject to  Ax ≥ b
            x ≥ 0
```

**Converting between forms:** Multiply both sides of an inequality by −1 to flip the direction; negate the objective to switch between minimize and maximize.

### Matrix Notation in Pyomo

```python
import numpy as np

c = [-12, -9]                      # negated for minimization
A = [[-1,0],[0,-1],[-1,-1],[-4,-2]]
b = [-1000,-1500,-1750,-4800]

m = pyo.ConcreteModel()
m.I = pyo.Set(initialize=range(2))
m.x = pyo.Var(m.I, domain=pyo.NonNegativeReals)
m.obj = pyo.Objective(expr=sum(c[i]*m.x[i] for i in m.I), sense=pyo.minimize)
```

### Absolute Value in Constraints

Replace `|g(x)| ≤ d` with two linear constraints:
```
 g(x) ≤ d
-g(x) ≤ d    (equivalently, g(x) ≥ -d)
```

**In objective:** To minimize `|g(x)|`, introduce `y+ ≥ 0, y- ≥ 0` such that `g(x) = y+ - y-`. Then minimize `y+ + y-`.

---

## 📅 Week 5: Linear Duality and Solution Methods

### Duality Intuition

For any maximization (primal) LP, there is a **dual** minimization LP. The dual provides **upper bounds** on the primal optimal value. When both are solved to optimality, their objective values are equal — this equality is the **optimality certificate**.

**Idea:** Assign multipliers to constraints such that any weighted combination of constraints certifies that no feasible primal solution can exceed a bound.

### Gaming PC Example (Duality Construction)

**Primal:**
```
maximize    5x1 + 4x2
subject to  2x1 + x2  ≤ 100   (assembly hours)
            x1  + x2  ≤ 40    (GPUs)
            x1, x2    ≥ 0
```

**Dual construction:** Choose multipliers y1, y2 ≥ 0 such that:
- 2y1 + y2 ≥ 5 (for x1 coefficient)
- y1  + y2 ≥ 4 (for x2 coefficient)

**Dual:**
```
minimize    100y1 + 40y2
subject to  2y1 + y2 ≥ 5
            y1  + y2 ≥ 4
            y1, y2   ≥ 0
```

### BIM Dual Problem

**Dual variables:** λ1 (silicon), λ2 (germanium), λ3 (plastic), λ4 (copper)

**Dual problem:**
```
minimize    1000λ1 + 1500λ2 + 1750λ3 + 4800λ4
subject to  λ1          + λ3 + 4λ4  ≥ 12   (coefficient of x1)
                   λ2   + λ3 + 2λ4  ≥ 9    (coefficient of x2)
            λ1, λ2, λ3, λ4           ≥ 0
```

```python
m = pyo.ConcreteModel("BIM dual")
m.l1 = pyo.Var(domain=pyo.NonNegativeReals)
m.l2 = pyo.Var(domain=pyo.NonNegativeReals)
m.l3 = pyo.Var(domain=pyo.NonNegativeReals)
m.l4 = pyo.Var(domain=pyo.NonNegativeReals)
m.obj = pyo.Objective(
    expr=1000*m.l1 + 1500*m.l2 + 1750*m.l3 + 4800*m.l4,
    sense=pyo.minimize
)
m.c1 = pyo.Constraint(expr=m.l1 + m.l3 + 4*m.l4 >= 12)
m.c2 = pyo.Constraint(expr=m.l2 + m.l3 + 2*m.l4 >= 9)
SOLVER.solve(m)
```

### Primal-Dual Correspondence

| Primal (minimize c^T x, Ax ≥ b, x ≥ 0) | Dual (maximize b^T y, A^T y ≤ c, y ≥ 0) |
|---|---|
| n decision variables | n dual constraints |
| m constraints | m dual variables |
| Objective coefficients c | Dual RHS |
| RHS b | Dual objective coefficients |
| Constraint matrix A | Transposed: A^T |

**For maximization primal:** dual is a minimization problem.

### Weak Duality Theorem

For any feasible primal solution x and any feasible dual solution λ:

- **(minimization primal):** dual objective ≤ primal objective → dual provides lower bound
- **(maximization primal):** dual objective ≥ primal objective → dual provides upper bound

Written: `ℓ(λ) ≤ v(P)` (for minimization)

### Strong Duality Theorem

**If both primal and dual have feasible solutions, their optimal objective values are equal:**

```
v(P) = v(D)
```

**Optimality certificate:** Find feasible x and λ where the primal value equals the dual value — this proves both are optimal. No further search needed.

### Shadow Prices

**Dual variables = shadow prices.** The dual variable for a resource constraint equals the **marginal value of that resource** — how much the optimal objective improves if one additional unit of that resource becomes available.

### The Simplex Method

1. Start at a **corner point (vertex)** of the feasible polytope
2. Move to an adjacent vertex with a strictly better objective
3. Stop when no adjacent vertex improves — this is the optimum (for LP)
4. Uses **slack variables** to convert inequalities to equalities
5. **Basic variables** — currently in the basis (nonzero); **non-basic** — set to zero
6. At optimality: no improving direction exists among all adjacent vertices

### Interior Point Method (Barrier Method)

Instead of walking along edges, path through the **interior** of the feasible region. A **barrier term** `−μ · Σ log(xi)` is added to the objective to prevent the solution from touching the boundary prematurely. As `μ → 0`, the solution converges to the true optimum. More efficient than Simplex for very large-scale problems.

### Multi-Period Planning

For a 12-month BIM production plan:
- Decision variables indexed over products and time periods: `m.P × m.T`
- Monthly budget constraint ≤ 5000, inventory ≤ 9000
- Holding cost: 0.05 per unit per month
- **Monthly inventory balance:** `inventory[t] = inventory[t-1] + production[t] - demand[t]`
- Objective: minimize total acquisition + holding cost

---

## 📅 Week 6: Linear Optimization Examples

### Indexed Sets in Pyomo

When a model has multiple similar variables (one per feature, product, route, etc.), use **indexed sets** and **indexed variables** instead of naming each separately.

```python
features = tasks.index.tolist()          # e.g., ["Gameplay", "UI/UX", "LiveOps", "Performance"]
m.T = pyo.Set(initialize=features)
m.x = pyo.Var(m.T, domain=pyo.NonNegativeReals)
```

**Indexed objective:**
```python
def objective_rule(m):
    return sum(tasks.loc[t, "Satisfaction"] * m.x[t] for t in m.T)
m.obj = pyo.Objective(rule=objective_rule, sense=pyo.maximize)
```

**Indexed constraint:**
```python
def dev_rule(m):
    return sum(tasks.loc[t, "DevHours"] * m.x[t] for t in m.T) <= limits["DevHours"]
m.dev_constraint = pyo.Constraint(rule=dev_rule)
```

### Pandas Integration Pattern

- Store problem data in a `pd.DataFrame` — rows are variables/items, columns are resource types
- Access data with `.loc[row_index, column_name]`
- Pass results back into a DataFrame for clean reporting

### Game Development Sprint Planning Example

**Objective:** Maximize total Player Satisfaction Score.

| Feature | Satisfaction | Dev (h) | Designer (h) | QA (h) |
|---|---|---|---|---|
| Gameplay | 55 | 10 | 2 | 4 |
| UI/UX | 35 | 4 | 8 | 2 |
| LiveOps | 40 | 6 | 3 | 5 |
| Performance | 25 | 8 | 1 | 6 |

**Resource limits:** Developer = 240 h, Designer = 160 h, QA = 140 h

Each `x[t]` ∈ [0,1] represents the fraction of feature `t` completed (continuous relaxation), or `domain=pyo.Binary` for an all-or-nothing decision.

**Complete Pyomo solution:**
```python
import pandas as pd
import pyomo.environ as pyo

tasks = pd.DataFrame({
    "Satisfaction": {"Gameplay": 55, "UI/UX": 35, "LiveOps": 40, "Performance": 25},
    "DevHours":     {"Gameplay": 10, "UI/UX":  4, "LiveOps":  6, "Performance":  8},
    "DesignerHours":{"Gameplay":  2, "UI/UX":  8, "LiveOps":  3, "Performance":  1},
    "QAHours":      {"Gameplay":  4, "UI/UX":  2, "LiveOps":  5, "Performance":  6},
})
limits = {"DevHours": 240, "DesignerHours": 160, "QAHours": 140}

m = pyo.ConcreteModel("Sprint Planning")
m.T = pyo.Set(initialize=tasks.index.tolist())
m.x = pyo.Var(m.T, domain=pyo.NonNegativeReals, bounds=(0, 1))

@m.Objective(sense=pyo.maximize)
def obj(m):
    return sum(tasks.loc[t, "Satisfaction"] * m.x[t] for t in m.T)

for resource in ["DevHours", "DesignerHours", "QAHours"]:
    m.add_component(
        resource,
        pyo.Constraint(expr=sum(tasks.loc[t, resource]*m.x[t] for t in m.T) <= limits[resource])
    )

SOLVER.solve(m)
```

---

## 📅 Week 7: Mixed-Integer Linear Optimization — Formulation and Modeling Techniques

### What Is MILP?

**MILP (Mixed-Integer Linear Programming)** — also called **MILO (Mixed-Integer Linear Optimization)** — is LP with some or all variables restricted to **integers** or **binary (0/1) values**.

| Property | LP | MILP |
|---|---|---|
| Variable types | Continuous only | Integer and/or binary |
| Computational complexity | Polynomial time | NP-hard in general |
| Rounding LP solution | Always works | May be infeasible or suboptimal |

### Integer and Binary Variables in Pyomo

```python
m.x = pyo.Var(domain=pyo.NonNegativeIntegers)   # integer ≥ 0
m.y = pyo.Var(domain=pyo.Binary)                 # y ∈ {0, 1}
```

### Example 1: Integer BIM

Same as the BIM LP but both chip quantities must be whole numbers. The LP optimal (650, 1100) happens to be integer — but with modified cost data, the LP relaxation may give a fractional solution that cannot simply be rounded.

### Example 2: Lab Seating (Binary Assignment)

6 students must be assigned to 6 PCs to minimize dissatisfaction. Binary variable: `x[s,p] = 1` if student `s` uses PC `p`.

**Constraints:**
- Each student gets exactly one PC: `sum_p x[s,p] = 1` for all s
- Each PC used by at most one student: `sum_s x[s,p] ≤ 1` for all p
- Special restriction (S3 must use PC5 or PC6): `x[S3,PC5] + x[S3,PC6] = 1`
- Adjacency restriction (S1 and S2 not on adjacent PCs): add via `ConstraintList`

```python
m.x = pyo.Var(m.S, m.P, domain=pyo.Binary)

@m.Constraint(m.S)
def one_pc_per_student(m, s):
    return sum(m.x[s,p] for p in m.P) == 1

@m.Constraint(m.P)
def one_student_per_pc(m, p):
    return sum(m.x[s,p] for s in m.S) <= 1
```

### Example 3: Knapsack / Feature Selection

Select a subset of features to maximize value within a fixed budget of hours.

```python
m.x = pyo.Var(m.I, domain=pyo.Binary)
m.value_obj = pyo.Objective(expr=sum(value[i]*m.x[i] for i in m.I), sense=pyo.maximize)
m.budget    = pyo.Constraint(expr=sum(hours[i]*m.x[i] for i in m.I) <= budget)
```

### MILP Modeling Techniques

**1. Discontinuous variable range** (variable is either 0 or in `[L, U]`):
- Introduce binary `y`
- `x ≤ U · y`  (forces x = 0 when y = 0)
- `x ≥ L · y`  (forces x ≥ L when y = 1)

**2. Big-M optional constraint** (constraint active iff `y = 1`):
```
x ≤ b + M · (1 - y)
```
- When `y=1`: `x ≤ b` (constraint is active)
- When `y=0`: `x ≤ b + M` (effectively inactive for large M)
- **Choose M as small as safely possible** — large M weakens the LP relaxation

**3. Fixed setup cost:**
- Cost = `k · y + c · x` (fixed cost k when production is nonzero)
- Add: `x ≤ U · y` (no production without paying setup cost)

**4. Either-Or constraints** (exactly one of two constraints must hold):
```
g1(x) ≤ b1 + M · z
g2(x) ≤ b2 + M · (1 - z)
```
Binary `z` selects which constraint is active.

**5. If-Then implication** ("if advanced feature is selected then basic must be too"):
```
y_advanced ≤ y_basic
```

**6. Product of two binary variables** (`y = x1 · x2`):
```
y ≤ x1
y ≤ x2
y ≥ x1 + x2 - 1
```

**7. Disjunctions (GDP — Generalized Disjunctive Programming):**
```python
@m.Disjunction(xor=True)
def either_or(m):
    return [
        [constraint_option_1a, constraint_option_1b],
        [constraint_option_2a, constraint_option_2b]
    ]
pyo.TransformationFactory("gdp.bigm").apply_to(m)
```
`xor=True` means exactly one disjunct is active. The `bigm` transformation converts it to Big-M form automatically.

---

## 📅 Week 8: Mixed-Integer Linear Optimization — Solution Methods and Examples

### Why MILP Is Hard

For LP, the Simplex Method finds the optimum in polynomial time. For MILP, the feasible set is no longer convex — the solution space contains isolated integer points. Brute-force enumeration of all integer combinations is exponential.

### Linear Relaxation

**Linear relaxation** = drop all integrality requirements and solve as an LP.

- For **maximization**: LP relaxation gives an **upper bound** on the MILP optimal
- For **minimization**: LP relaxation gives a **lower bound**
- The LP relaxation is always at least as good as the integer optimal
- **You cannot simply round** the LP solution — it may be infeasible or far from optimal

### Branch and Bound Algorithm

The standard algorithm for solving MILP problems exactly.

**Algorithm:**
1. Solve the LP relaxation of the current node
2. If LP is infeasible → **prune** this branch
3. If LP optimal value is worse than the best known integer solution (incumbent) → **prune**
4. If LP gives an **all-integer** solution → update incumbent
5. Otherwise, select a **fractional variable** `xi` (value not integer)
6. **Branch:** create two child nodes by adding:
   - **Left branch:** `xi ≤ floor(fractional_value)`
   - **Right branch:** `xi ≥ ceiling(fractional_value)`
7. Recurse

**B&B Example:** maximize `x1 + x2` subject to `2x1+x2 ≤ 10, x1+2x2 ≤ 8, x1,x2 ≥ 0, integer`.
- LP relaxation: fractional → branch on x1
- Explore both subtrees, prune dominated nodes
- Integer optimal = 11

### Cutting Planes

Add a **valid inequality (cut)** that:
- Eliminates the current fractional LP solution
- Does **not** remove any integer-feasible point

Re-solve the LP — the new vertex may be integer. Repeat.

**Chvátal-Gomory (CG) Cuts:**
A systematic method: take a nonneg linear combination of constraints, then **round down the RHS**. Any integer-feasible point satisfies the rounded version. Provides a cut that removes the fractional LP solution without excluding integer solutions.

### Branch and Cut

Modern MILP solvers combine both: **Branch and Bound + Cutting Planes + Primal Heuristics**. Cutting planes tighten the LP relaxation at each node, reducing the branching required.

### Tight vs. Loose Formulations

| Formulation | LP Relaxation | Solving Speed |
|---|---|---|
| Loose | Far from convex hull of integer points | Slow — requires many branch steps |
| Tight | Close to convex hull | Fast — LP solution nearly integer |
| **Ideal** | LP relaxation = convex hull | LP gives integer solution directly |

**Convex hull of integer solutions** — the smallest convex set containing all integer feasible points. If the LP relaxation exactly describes this, Branch and Bound needs zero branching. This happens automatically for network flow problems with TU matrices (see Week 9).

### Facility Location Problem

**Decide which warehouses to open and which cities each warehouse serves.**

- Binary `yi = 1` if warehouse i is opened
- Binary `x_ij = 1` if warehouse i serves city j

**Model:**
```
minimize    Σ_i (fixed_cost_i · yi) + Σ_ij (delivery_cost_ij · x_ij)
subject to  Σ_i x_ij = 1            for all j    (each city served)
            x_ij ≤ yi               for all i,j   (only open warehouses can serve)
            yi, x_ij ∈ {0,1}
```

---

## 📅 Week 9: Network Optimization — Introduction and Totally Unimodular Matrices

### Networks and Flows

A **network** consists of:
- **Nodes (vertices)** — locations, entities, states
- **Edges (arcs)** — connections between nodes, carrying flow

**Why network models?**
- Many real-world problems have natural network structure
- Highly efficient specialized algorithms exist
- The **TU property** guarantees integer LP solutions — no branch and bound needed for integer capacities

### Minimum-Cost Flow Problem

The most general single-commodity network flow problem.

**Each node j has:**
- `b_j > 0` — supply (node produces flow)
- `b_j < 0` — demand (node consumes flow)
- `b_j = 0` — transshipment (pure relay)

**Each edge (i,j) has:**
- `u_ij` — capacity (maximum flow)
- `c_ij` — unit cost

**Decision variable:** `x_ij` = amount of flow on edge (i,j)

**Model:**
```
minimize    Σ_(i,j) c_ij · x_ij
subject to  Σ_j x_ij - Σ_j x_ji = b_i    for all i    (flow conservation)
            x_ij ≤ u_ij                    for all (i,j) (capacity)
            x_ij ≥ 0                       for all (i,j) (nonnegativity)
```

**Flow conservation:** At every node, outgoing flow minus incoming flow equals net supply/demand.

```python
model.x = pyo.Var(network["edges"], domain=pyo.NonNegativeReals)

@model.Objective(sense=pyo.minimize)
def objective(m):
    return sum(data["c"] * m.x[e] for e, data in network["edges"].items())

@model.Expression(network["nodes"])
def incoming_flow(m, j):
    return sum(m.x[i,j] for i in network["nodes"] if (i,j) in network["edges"])

@model.Constraint(network["nodes"])
def flow_conservation(m, j):
    return m.outgoing_flow[j] - m.incoming_flow[j] == network["nodes"][j]["b"]

@model.Constraint(network["edges"])
def flow_upper_bound(m, *e):
    return m.x[e] <= network["edges"][e]["u"]
```

### Totally Unimodular (TU) Matrices

**Definition:** A matrix A is **Totally Unimodular (TU)** if every square submatrix has determinant in {0, +1, −1}.

**Key theorem:** If A is TU and b is an **integer vector**, then the LP:
```
minimize    c^T x
subject to  Ax ≤ b,  x ≥ 0
```
has an **integer optimal solution** (if an optimal exists).

**Why this matters:** The constraint matrix of any network flow problem is TU. Therefore:
> For network problems with integer capacities, solving the LP relaxation gives an integer solution automatically — **no branch and bound needed**.

### Student Lab Group Assignment as a Network Flow

**Setup:** Assign students from different programs to lab groups, respecting per-group capacity and a limit `kmax` on how many students from the same program can be in one group.

```python
import networkx as nx

def model_as_network(program_students, group_capacities, kmax):
    programs = [f"program_{i}" for i in range(len(program_students))]
    groups   = [f"group_{j}"   for j in range(len(group_capacities))]
    G = nx.DiGraph()
    # Source → programs (capacity = number of students per program)
    G.add_edges_from([("door", p, {"capacity": n})
                      for p, n in zip(programs, program_students)])
    # Programs → groups (capacity = kmax)
    G.add_edges_from([(p, g) for p in programs for g in groups], capacity=kmax)
    # Groups → sink (capacity = group size)
    G.add_edges_from([(g, "seat", {"capacity": c})
                      for g, c in zip(groups, group_capacities)])
    return G

G = model_as_network(program_students, group_capacities, kmax)
flow_value, flow_dict = nx.maximum_flow(G, "door", "seat")
# If flow_value == total students, assignment is feasible
```

---

## 📅 Week 10: Special Network Problems and Examples

### Why Transform Problems Into Networks?

- Specialized algorithms (Dijkstra, max-flow) are orders of magnitude faster than generic LP for large instances
- TU property guarantees integer LP solutions with no extra work
- Well-studied complexity bounds; many polynomial-time algorithms

### Shortest Path Problem

Find the minimum-cost directed path from source `s` to sink `t`.

**Network flow formulation:** `b_s = 1` (source produces 1 unit), `b_t = −1` (sink absorbs 1 unit), `b_j = 0` for all other nodes.

### Dijkstra's Algorithm

Finds shortest paths from a single source to all other nodes. Complexity: **O(n²)** basic, **O(m + n log n)** with a priority queue.

**Procedure:**
1. Initialize: source label = 0, all others = ∞
2. Mark source as **permanent**
3. For each temporary neighbor `j` of the latest permanent node `i`: update label `s_j = min(s_j, s_i + c_ij)`
4. Select the temporary node with the **smallest label** → make permanent
5. Repeat until sink is reached

**Worked example** (edges: a→b=10, a→d=5, d→b=3, d→e=2, b→c=1, e→c=5, e→b=8):

| Step | Permanent | Labels (a,b,c,d,e) | Action |
|---|---|---|---|
| 0 | a | (0,∞,∞,∞,∞) | Start |
| 1 | d | (0,10,∞,5,∞) | Process a |
| 2 | e | (0,8,14,5,7) | Process d: update b=8, e=7 |
| 3 | b | (0,8,12,5,7) | Process e: update c=12 |
| 4 | c | (0,8,9,5,7) | Process b: update c=9 |

**Shortest path: a → d → b → c, cost = 5 + 3 + 1 = 9**

### Transportation Problem

Special case of min-cost flow: **bipartite network** with suppliers (supply) and customers (demand).

- Constraint matrix is TU → LP gives integer solution directly
- No branch and bound needed

### Gasoline Distribution Example

3 terminals supply 8 franchisees. Minimize total shipping cost.

```python
def transport(supply, demand, rates):
    m = pyo.ConcreteModel("Gasoline distribution")
    m.SOURCES = pyo.Set(initialize=rates.columns)
    m.DESTINATIONS = pyo.Set(initialize=rates.index)
    m.x = pyo.Var(m.DESTINATIONS, m.SOURCES, domain=pyo.NonNegativeReals)

    @m.Param(m.DESTINATIONS, m.SOURCES)
    def Rates(m, dst, src):
        return rates.loc[dst, src]

    @m.Objective(sense=pyo.minimize)
    def total_cost(m):
        return sum(m.Rates[dst,src] * m.x[dst,src]
                   for dst in m.DESTINATIONS for src in m.SOURCES)

    @m.Constraint(m.SOURCES)
    def supply_constraint(m, src):
        return sum(m.x[dst,src] for dst in m.DESTINATIONS) <= supply[src]

    @m.Constraint(m.DESTINATIONS)
    def demand_constraint(m, dst):
        return sum(m.x[dst,src] for src in m.SOURCES) == demand[dst]

    m.dual = pyo.Suffix(direction=pyo.Suffix.IMPORT)
    return m

m = transport(supply, demand, rates / 100)
SOLVER.solve(m)
```

`m.dual` imports shadow prices — these show the marginal cost of supplying one more unit to each destination.

### Maximum-Weight Bipartite Matching

**Two groups I and J; match each i to exactly one j to maximize total weight.**

```
maximize    Σ_ij c_ij · x_ij
subject to  Σ_j x_ij  = 1     for all i    (each i matched once)
            Σ_i x_ij  ≤ 1     for all j    (each j matched at most once)
            x_ij ∈ {0,1}
```

Because the constraint matrix is TU, solving the LP gives a 0/1 solution directly.

### Max-Flow / Min-Cut Theorem

**Maximum flow:** Find the max flow from source `s` to sink `t`, respecting edge capacities.

**Min-cut:** A partition of nodes into sets V_s (containing s) and V_t (containing t). Cut capacity = sum of capacities of edges from V_s to V_t.

> **Max-flow = Min-cut capacity.** The bottleneck in the network limits total flow.

### Traveling Salesman Problem (TSP)

Visit each city exactly once, return to start, minimize total distance.

**Model:**
```
minimize    Σ_ij c_ij · x_ij
subject to  Σ_i x_ij = 1         for all j    (each city entered once)
            Σ_j x_ij = 1         for all i    (each city exited once)
            Σ_{i,j ∈ S} x_ij ≤ |S| − 1   for each proper subset S   (subtour elimination)
            x_ij ∈ {0,1}
```

**TSP is NP-hard.** The subtour elimination constraints are exponential in number. Modern approaches use branch-and-cut with dynamically added subtour inequalities.

### Graph Coloring (Exam Scheduling)

Assign colors (time slots) to nodes (courses) so that no two adjacent nodes (courses sharing students) share a color (time slot). Minimize the number of colors.

```python
m.x = pyo.Var(m.nodes, m.colors, domain=pyo.Binary)    # x[i,k]=1 if course i uses slot k
m.w = pyo.Var(m.colors, domain=pyo.Binary)              # w[k]=1 if slot k is used

m.obj = pyo.Objective(expr=sum(m.w[k] for k in m.colors), sense=pyo.minimize)

@m.Constraint(m.edges, m.colors)
def no_conflict(m, i, j, k):
    return m.x[i,k] + m.x[j,k] <= 1

@m.Constraint(m.nodes)
def one_slot(m, i):
    return sum(m.x[i,k] for k in m.colors) == 1

@m.Constraint(m.nodes, m.colors)
def slot_activation(m, i, k):
    return m.x[i,k] <= m.w[k]
```

---

## 📅 Week 11: Convex Optimization — Introduction, Modeling and Verification

### Position of Convex Optimization

```
Linear Optimization ⊂ Convex Optimization ⊂ General Nonlinear Optimization
```

**Key insight:** Nonlinear ≠ nonconvex. A nonlinear problem may still be convex, and convex problems are far easier to solve than general nonlinear ones.

### Convex Sets

**Definition:** A set S is **convex** if for any two points x, y ∈ S and any θ ∈ [0,1]:

```
θx + (1−θ)y ∈ S
```

The entire line segment connecting x and y lies inside S.

**Convex examples:** any line, any half-space (Ax ≤ b), any ball, any polytope, a single point, the empty set.

**Non-convex example:** a set with a hole, a donut shape, a non-star-shaped region.

### Convex Functions

**Definition:** A function f is **convex** if for any x, y in its domain and θ ∈ [0,1]:

```
f(θx + (1−θ)y) ≤ θf(x) + (1−θ)f(y)
```

**Geometrically:** the chord connecting any two points on the graph lies **above or on** the graph.

**Concave function:** f is concave if −f is convex. The chord lies **below** the graph.

**Strictly convex:** strict inequality for x ≠ y and 0 < θ < 1 → unique global minimum.

### Fundamental Property of Convex Optimization

> **If f is convex and the feasible set S is convex, then every local minimum is a global minimum.**

This makes convex optimization far more tractable than general nonlinear optimization.

### Common Convex Functions

| Function | Condition | Convex? |
|---|---|---|
| ax + b | any a | Yes (also concave) |
| x² | — | Yes |
| ax² + bx + c | a ≥ 0 | Yes |
| eˣ | — | Yes |
| −log(x) | x > 0 | Yes |
| ‖x‖, ‖x‖² | — | Yes |

**Concave:** −x², log(x), √x, −eˣ

### Hessian Matrix

The **Hessian** ∇²f(x) is the matrix of all second-order partial derivatives:

```
∇²f(x)_{ij} = ∂²f / (∂x_i ∂x_j)
```

**Convexity criterion:**
- f is **convex** ⟺ ∇²f(x) is **positive semidefinite (PSD)** for all x
- f is **strictly convex** ⟺ ∇²f(x) is **positive definite (PD)** for all x

### Matrix Definiteness

For symmetric matrix Q and any nonzero vector t:

| Type | Condition | Meaning |
|---|---|---|
| **Positive Definite (PD)** | t^T Q t > 0 | Strictly upward curvature in all directions |
| **Positive Semidefinite (PSD)** | t^T Q t ≥ 0 | Never negative; may be flat |
| **Negative Definite (ND)** | t^T Q t < 0 | Strictly downward |
| **Negative Semidefinite (NSD)** | t^T Q t ≤ 0 | Never positive |
| **Indefinite** | Mixed signs | Nonconvex — saddle point structure |

**Examples:**
- Q = [[2,−1],[−1,2]] → PD (eigenvalues both positive)
- Q = [[1,−1],[−1,1]] → PSD (has a zero eigenvalue)
- Q = [[−3,2],[2,1]] → Indefinite

**For f(x) = x^T Q x:** convex ⟺ Q ≽ 0; strictly convex ⟺ Q ≻ 0.

### Operations That Preserve Convexity

1. **Nonneg weighted sum:** if all f_i are convex and w_i ≥ 0, then Σ w_i f_i is convex
2. **Linear transformation:** if f is convex, then g(x) = f(Ax+b) is convex
3. **Pointwise maximum:** if all f_i are convex, then max{f_1, ..., f_m} is convex
4. **Composition:** if f is convex and g is convex and nondecreasing, then g(f(x)) is convex

### Worked Example: Verifying Convexity

**f(x1, x2) = x1² + 4x2²**

Hessian:
```
∇²f = [[2, 0],
        [0, 8]]
```

Check PSD: `t^T ∇²f t = 2t1² + 8t2² ≥ 0` for all t → ✔ PSD → f is **convex**.

Global minimum at (0, 0) — the unique point where gradient = 0.

---

## 📅 Week 12: Duality for Convex Optimization and Solution Methods

### Lagrangian Relaxation (LP Case)

**Primal:** minimize c^T x subject to Ax ≤ b, x ≥ 0

Move inequality constraints into the objective with multipliers λ ≥ 0:

```
L(x, λ) = c^T x + λ^T (Ax − b)
```

- The term λ^T(Ax−b) penalizes constraint violations
- For feasible x (Ax ≤ b): λ^T(Ax−b) ≤ 0, so L(x,λ) ≤ c^T x
- Therefore L(x,λ) ≤ c^T x → lower bound on the primal

### Lagrangian Dual Function

```
ℓ(λ) = inf_{x ≥ 0} L(x,λ) = inf_{x ≥ 0} [c^T x + λ^T(Ax − b)]
```

- If `c^T + λ^T A ≥ 0` component-wise: `ℓ(λ) = −λ^T b` (finite)
- Otherwise: `ℓ(λ) = −∞` (Lagrangian unbounded below)

### General Convex Problem and Lagrangian

**Problem:**
```
minimize    f(x)
subject to  g_i(x) ≤ 0    i = 1,...,m
            x ∈ X
```

**Lagrangian:**
```
L(x, λ) = f(x) + Σ_i λ_i · g_i(x)
```

Where λ_i ≥ 0 (one multiplier per inequality constraint).

**Dual function:**
```
ℓ(λ) = inf_{x ∈ X} L(x, λ)
```

**Dual problem:**
```
maximize    ℓ(λ)
subject to  λ ≥ 0
```

### 6-Step Procedure to Derive the Dual

1. Write the primal problem
2. Rewrite all inequalities as `g_i(x) ≤ 0`
3. Assign multiplier `λ_i ≥ 0` to each inequality
4. Form `L(x,λ) = f(x) + Σ λ_i · g_i(x)`
5. Minimize L over x: `ℓ(λ) = inf_x L(x,λ)`
6. Dual: `maximize ℓ(λ)` subject to `λ ≥ 0`

### Worked Example: Deriving the Convex Dual

**Primal:** minimize x² subject to x ≥ 1, x ≤ 3

Rewrite: g1(x) = 1−x ≤ 0, g2(x) = x−3 ≤ 0

**Lagrangian:** `L(x,λ1,λ2) = x² + λ1(1−x) + λ2(x−3) = x² + (λ2−λ1)x + λ1−3λ2`

**Minimize over x:** `2x + (λ2−λ1) = 0` → `x* = (λ1−λ2)/2`

**Dual function:** `ℓ(λ1,λ2) = λ1−3λ2 − (λ1−λ2)²/4`

**Dual problem:** maximize `[λ1 − 3λ2 − (λ1−λ2)²/4]` subject to λ1,λ2 ≥ 0

**Optimal dual:** λ1* = 2, λ2* = 0 → `v(D) = 2 − 1 = 1 = v(P)` ✔ Strong duality holds.

### Weak Duality (Convex)

For any feasible primal x and feasible dual λ:

```
ℓ(λ) ≤ f(x)     equivalently:     v(D) ≤ v(P)
```

**Proof sketch:**
1. `ℓ(λ) = inf_y L(y,λ) ≤ L(x,λ)` (infimum ≤ any particular value)
2. `L(x,λ) = f(x) + λ^T g(x) ≤ f(x)` (since g(x) ≤ 0 and λ ≥ 0)
3. Therefore `ℓ(λ) ≤ f(x)`

### Strong Duality and Duality Gap

**Duality gap:** `v(P) − v(D) ≥ 0` (always, by weak duality).

**Strong duality:** `v(P) = v(D)` — the gap is zero.

**Optimality certificate:** Find feasible x and λ with `f(x) = ℓ(λ)`. By weak duality, ℓ(λ) ≤ v(D) ≤ v(P) ≤ f(x), so everything equals v(P) = v(D). Both are optimal.

### Slater's Condition

**Slater's condition** is satisfied if there exists a point x̂ such that:
- All nonlinear inequality constraints are **strictly satisfied**: `g_i(x̂) < 0`
- For linear inequalities, `≤ 0` is sufficient (not strictly required)
- All equality constraints are satisfied

> **If Slater's condition holds → strong duality holds: v(P) = v(D)**

Slater's condition is easy to verify in practice. If satisfied, dual methods and KKT conditions can be applied with confidence.

### KKT Conditions (Karush-Kuhn-Tucker)

For: minimize f(x) subject to g_i(x) ≤ 0 (i=1,...,m) and h_j(x) = 0 (j=1,...,p)

**Lagrangian:** `L(x,λ,μ) = f(x) + Σ λ_i g_i(x) + Σ μ_j h_j(x)`

Where λ_i ≥ 0 (inequality multipliers) and μ_j ∈ ℝ (equality multipliers).

**The four KKT conditions:**

| # | Name | Condition |
|---|---|---|
| 1 | **Stationarity** | ∇f(x) + Σ λ_i ∇g_i(x) + Σ μ_j ∇h_j(x) = 0 |
| 2 | **Primal Feasibility** | g_i(x) ≤ 0 for all i; h_j(x) = 0 for all j |
| 3 | **Dual Feasibility** | λ_i ≥ 0 for all i |
| 4 | **Complementary Slackness** | λ_i · g_i(x) = 0 for all i |

**Complementary slackness meaning:** Either the constraint is inactive (g_i(x) < 0), in which case λ_i = 0; or the multiplier is positive (λ_i > 0), in which case the constraint is active (g_i(x) = 0). A constraint either contributes to the optimum or its price is zero.

**When KKT is sufficient:**
- For convex problems with Slater's condition: KKT is **necessary AND sufficient** for global optimality.
- A point satisfying all four KKT conditions is the global optimum.

### Summary Table

| Theorem | Statement | When it holds |
|---|---|---|
| Weak duality | v(D) ≤ v(P) | Always |
| Strong duality | v(D) = v(P) | Slater's condition satisfied |
| KKT necessary | Global min satisfies KKT | Slater's condition satisfied |
| KKT sufficient | KKT point is global min | Problem is convex |

---

## 📅 Week 13: Convex Optimization Examples

### Data Center Server Load Optimization

**Problem:** Distribute 120 units of workload among 5 servers to minimize total energy consumption, where energy cost grows quadratically with load.

**Server data:**

| Server | Energy Cost c_s | Capacity M_s |
|---|---|---|
| A | 0.08 | 40 |
| B | 0.06 | 50 |
| C | 0.10 | 35 |
| D | 0.07 | 45 |
| E | 0.09 | 30 |

**Decision variables:** x_s = workload units assigned to server s

**Model:**
```
minimize    Σ_s c_s · x_s²
subject to  Σ_s x_s ≥ 120          (minimum total workload)
            x_s ≤ M_s   for all s   (capacity limits)
            x_s ≥ 0     for all s
```

**Why quadratic objective?** Doubling workload quadruples energy (x² grows faster than x). This encourages spreading load across all servers. Since each c_s > 0, each term c_s·x_s² is convex; the sum of convex functions is convex. Linear constraints define a convex feasible region. → This is a **convex optimization problem with a unique global minimum**.

### Complete Pyomo Implementation

```python
import pyomo.environ as pyo

model = pyo.ConcreteModel("Data_Center_Server_Load_Optimization")

servers = ["Server A", "Server B", "Server C", "Server D", "Server E"]
energy_cost = {"Server A": 0.08, "Server B": 0.06, "Server C": 0.10,
               "Server D": 0.07, "Server E": 0.09}
max_workload = {"Server A": 40,   "Server B": 50,   "Server C": 35,
                "Server D": 45,   "Server E": 30}
required_workload = 120

model.S = pyo.Set(initialize=servers)
model.x = pyo.Var(model.S, domain=pyo.NonNegativeReals)

def objective_rule(model):
    return sum(energy_cost[s] * model.x[s]**2 for s in model.S)
model.objective = pyo.Objective(rule=objective_rule, sense=pyo.minimize)

def workload_rule(model):
    return sum(model.x[s] for s in model.S) >= required_workload
model.workload_requirement = pyo.Constraint(rule=workload_rule)

def capacity_rule(model, s):
    return model.x[s] <= max_workload[s]
model.capacity_constraint = pyo.Constraint(model.S, rule=capacity_rule)

solver = pyo.SolverFactory("ipopt")
results = solver.solve(model)

print("Solver status:", results.solver.status)
print("Termination condition:", results.solver.termination_condition)

for s in model.S:
    print(f"{s}: {pyo.value(model.x[s]):.2f} workload units")

print("Total workload:", sum(pyo.value(model.x[s]) for s in model.S))
print("Minimum total energy:", pyo.value(model.objective))
```

### Optimal Solution (Example Output)

| Server | Workload Assigned |
|---|---|
| Server A (c=0.08) | 23.39 |
| Server B (c=0.06) | 31.18 |
| Server C (c=0.10) | 18.70 |
| Server D (c=0.07) | 26.72 |
| Server E (c=0.09) | 20.79 |

**Interpretation:** The optimizer favors Server B (lowest coefficient 0.06) and Server D (0.07) — they receive more workload. However, the quadratic cost penalizes heavy overloading, so workload is spread across all servers rather than assigning everything to the cheapest one.

### Solver Choice: IPOPT vs. HiGHS

| Solver | Problem Type | Pyomo Call |
|---|---|---|
| HiGHS (`appsi_highs`) | Linear (LP, MILP) | `pyo.SolverFactory("appsi_highs")` |
| **IPOPT** | **Nonlinear convex** | `pyo.SolverFactory("ipopt")` |
| Mosek / Gurobi | Conic (SOCO, SDP) | `pmo.SolverFactory("mosek_direct")` |

IPOPT = **Interior Point OPTimizer**. Uses barrier methods to navigate the interior of the feasible region toward the optimal solution. Required whenever the objective or constraints contain nonlinear terms (such as x²).

---

## 📅 Week 14: Conic Optimization

### Position in the Optimization Hierarchy

```
Linear Optimization ⊂ Conic Optimization ⊂ Convex Optimization
```

Conic optimization is:
1. **More expressive** than LP — models nonlinear constraints efficiently
2. **More structured** than general convex optimization — duality is systematic (like LP)
3. Solved by specialized interior-point algorithms with strong theoretical guarantees

### General Conic Problem Form

```
minimize    c^T x
subject to  A_i x + b_i ∈ K_i    for i = 1,...,m
            x ∈ ℝⁿ
```

Where K_i are **cones**. The constraint `A_i x + b_i ∈ K_i` means the linear image of x must lie inside the cone K_i.

### Motivating Example: Economic Order Quantity (EOQ)

**Parameters:** h = annual holding cost/item, c = fixed cost/order, d = annual demand, x = order quantity

**Total annual cost:**
```
f(x) = hx/2 + cd/x
```

This is a U-shaped (convex) function. **Analytical optimal:**
```
EOQ = x* = sqrt(2cd/h)
f*  = sqrt(2cdh)
```

For h=0.75, c=500, d=10,000: **EOQ ≈ 3651.48 units, optimal cost ≈ 2738.61**

**When a warehouse capacity constraint is added** (`Σ x_i ≤ C` for multiple products), the analytical solution no longer works → conic optimization.

### Linearization of EOQ

Introduce auxiliary variable y ≥ 1/x (i.e., add constraint xy ≥ 1). Problem becomes:

```
minimize    hx/2 + cd·y
subject to  xy ≥ 1
            x, y > 0
```

Now: **linear objective + hyperbolic constraint**.

### Reformulation as Second-Order Cone (SOCO)

Using the algebraic identity:
```
xy ≥ 1
⟺ 4xy ≥ 4
⟺ (x+y)² − (x−y)² ≥ 4
⟺ ||(2, x−y)||₂ ≤ x+y
```

**EOQ as a SOCO problem:**
```
minimize    hx/2 + cd·y
subject to  ||(2, x−y)||₂ ≤ x+y
            x, y ≥ 0
```

This is a **Second-Order Cone constraint** — now the problem is conic.

### Cones: Definitions

**Cone:** A set C ⊆ ℝⁿ is a cone if for every x ∈ C and θ ≥ 0: θx ∈ C (closed under positive scaling).

**Convex cone:** A cone C is convex if for any x1, x2 ∈ C and θ1, θ2 ≥ 0: θ1·x1 + θ2·x2 ∈ C.

### Type 1: Nonnegative Orthant (LP Cone)

```
K_LP = ℝ₊ᵐ = {x ∈ ℝᵐ : x ≥ 0}   (all nonneg components)
```

The standard LP can be written in conic form:
```
minimize    c^T x
subject to  Ax − b ∈ K_LP
            x ∈ ℝ₊ⁿ
```

### Type 2: Second-Order Cone (Lorentz Cone / Ice-Cream Cone)

```
K_SOCO^(n+1) = {(x, t) ∈ ℝⁿ⁺¹ : ||x||₂ ≤ t}
             = {(x, t) : sqrt(x1² + x2² + ... + xn²) ≤ t}
```

This allows modeling quadratic-like constraints as conic constraints. It is a convex cone.

### Type 3: Positive Semidefinite (PSD) Cone

```
K_PSD = {X ∈ ℝⁿˣⁿ : X is symmetric and positive semidefinite}
```

PSD cone enables **Semidefinite Programming (SDP)** — used for eigenvalue optimization, relaxations of combinatorial problems (e.g., MAX-CUT), and robust optimization.

### Conic Hierarchy

| Cone Type | Problem Class | Expressiveness |
|---|---|---|
| Nonneg orthant K_LP | Linear Programming | Least expressive |
| Second-Order Cone K_SOCO | SOCO / Quadratically Constrained | Medium |
| PSD Cone K_PSD | Semidefinite Programming | Most expressive |

```
LP ⊂ SOCO ⊂ SDP ⊂ Convex Optimization
```

### Pyomo Conic Implementation

Pyomo's `kernel` library provides direct conic constraint support (requires Mosek or Gurobi):

**Using `conic.quadratic` (standard form):**
```python
import pyomo.kernel as pmo
import numpy as np

h = 0.75
c = 500.0
d = 10000.0

m = pmo.block()
m.u = pmo.variable(lb=0)
m.v = pmo.variable(lb=0)
m.t = pmo.variable(lb=0)

m.u_eq = pmo.constraint(m.u == 2)               # fix u = 2
m.q    = pmo.conic.quadratic(m.t, [m.u, m.v])  # u² + v² ≤ t²

m.eoq  = pmo.objective(((h + 2*c*d)*m.t + (h - 2*c*d)*m.v) / 4)

SOLVER = pmo.SolverFactory("mosek_direct")
SOLVER.solve(m)
```

**Using `conic.rotated_quadratic` (more accurate, directly models xy ≥ 1):**
```python
m = pmo.block()
m.x = pmo.variable(lb=0)
m.y = pmo.variable(lb=0)
m.q = pmo.conic.rotated_quadratic.as_domain(m.x, m.y, [np.sqrt(2)])
# represents: (sqrt(2))² ≤ 2·x·y, i.e., xy ≥ 1

m.eoq = pmo.objective(h*m.x/2 + c*d*m.y)
SOLVER.solve(m)
# Returns EOQ = 3651.48, matching analytical solution exactly
```

`conic.rotated_quadratic` represents: `Σ z_i² ≤ 2·r1·r2` with `r1, r2 ≥ 0`

### Conic Dual

A major advantage of conic formulations: the dual is constructed **systematically** (like LP dual), without full Lagrangian derivations.

**For the EOQ SOCO problem, the dual is:**
```
maximize    −2λ₁
subject to  [h/2, cd]^T = [[0,1,1],[0,−1,1]]^T · [λ₁,λ₂,λ₃]^T + [μ₁,μ₂]^T
            ||(λ₁, λ₂)||₂ ≤ λ₃                  (SOCO constraint)
            [μ₁,μ₂]^T ∈ ℝ₊²                     (nonneg)
```

Structure: same as an LP dual but with SOCO constraints replacing simple nonnegativity.

### Computational Advantages of Conic Optimization

The same problem formulated as **conic** (rather than general convex) can be solved significantly faster because:
1. Specialized interior-point algorithms exist for conic problems
2. Duality is simpler and more transparent — no custom Lagrangian needed
3. Modern solvers (Mosek, Gurobi) have highly optimized conic solvers

---

## Quick Reference: Pyomo Cheat Sheet

```python
import pyomo.environ as pyo

# ── Model ──────────────────────────────────────────────────────────
m = pyo.ConcreteModel("name")

# ── Sets ───────────────────────────────────────────────────────────
m.I = pyo.Set(initialize=[1, 2, 3])

# ── Variables ──────────────────────────────────────────────────────
m.x = pyo.Var(domain=pyo.NonNegativeReals)
m.x = pyo.Var(domain=pyo.NonNegativeIntegers)
m.x = pyo.Var(domain=pyo.Binary)
m.x = pyo.Var(bounds=(0, None))
m.x = pyo.Var(m.I, domain=pyo.NonNegativeReals)   # indexed

# ── Objectives ─────────────────────────────────────────────────────
m.obj = pyo.Objective(expr=..., sense=pyo.minimize)
m.obj = pyo.Objective(expr=..., sense=pyo.maximize)
@m.Objective(sense=pyo.maximize)
def obj(m): return ...

# ── Constraints ────────────────────────────────────────────────────
m.c = pyo.Constraint(expr=... <= ...)
@m.Constraint()
def my_con(m): return ... <= ...
@m.Constraint(m.I)
def indexed_con(m, i): return ... <= ...
m.cl = pyo.ConstraintList()
m.cl.add(expr=... <= ...)

# ── Expressions ────────────────────────────────────────────────────
m.e = pyo.Expression(expr=...)
@m.Expression(m.I)
def indexed_expr(m, i): return ...

# ── Parameters ─────────────────────────────────────────────────────
@m.Param(m.I)
def param(m, i): return data[i]

# ── Solve ──────────────────────────────────────────────────────────
SOLVER = pyo.SolverFactory("appsi_highs")         # LP / MILP
SOLVER = pyo.SolverFactory("ipopt")               # Nonlinear convex
results = SOLVER.solve(m)
results = SOLVER.solve(m, tee=True)               # show solver log

# ── Access values ──────────────────────────────────────────────────
pyo.value(m.x)
pyo.value(m.x[i])

# ── Dual values (shadow prices) ────────────────────────────────────
m.dual = pyo.Suffix(direction=pyo.Suffix.IMPORT)
# After solving: m.dual[m.my_constraint]

# ── GDP / Disjunctions ─────────────────────────────────────────────
@m.Disjunction(xor=True)
def either_or(m):
    return [[constraint_A1, constraint_A2],
            [constraint_B1, constraint_B2]]
pyo.TransformationFactory("gdp.bigm").apply_to(m)
```

---

## Quick Reference: Key Mathematical Results

### LP Duality

```
Primal:  max c^T x    subject to Ax ≤ b,   x ≥ 0
Dual:    min b^T y    subject to A^T y ≥ c, y ≥ 0

Weak duality:    c^T x ≤ b^T y   for any feasible x, y
Strong duality:  c^T x* = b^T y* at optimality
```

### Convexity Tests

```
Hessian ∇²f(x) ≽ 0 (PSD) everywhere → f is convex
Hessian ∇²f(x) ≻ 0 (PD)  everywhere → f is strictly convex
```

### KKT Conditions (Summary)

```
1. Stationarity:          ∇f + Σ λ_i ∇g_i + Σ μ_j ∇h_j = 0
2. Primal Feasibility:    g_i(x) ≤ 0,  h_j(x) = 0
3. Dual Feasibility:      λ_i ≥ 0
4. Complementary Slack:   λ_i · g_i(x) = 0
```

### Slater's Condition → Strong Duality

If there exists a strictly interior feasible point, then `v(P) = v(D)` and KKT conditions are necessary and sufficient for global optimality.

### TU Property → Free Integer Solutions

If the constraint matrix A is Totally Unimodular and b is integer, then the LP gives an integer optimal solution automatically. Applies to all network flow problems.

### Conic Hierarchy

```
LP ⊂ SOCO ⊂ SDP ⊂ Convex Optimization
```
