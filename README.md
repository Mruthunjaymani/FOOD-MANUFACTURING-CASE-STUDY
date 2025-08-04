# 🛢️ Food-Oil Blending with Gurobi  
*A mini-case study from* **Model Building in Mathematical Programming** *by H. P. Williams (5th ed., §3 Food Manufacture 1 & 2)*

---

## Problem in a Nutshell
A food manufacturer blends five different raw oils into a single edible-oil product over six months.  
The goal is to **maximise profit** while juggling:

1. **Purchasing costs** that fluctuate month-to-month  
2. **Storage costs** (£5 / ton · month, ≤ 1 000 t on hand per oil)  
3. **Refining capacity**  
   * Vegetable oils (Veg 1, Veg 2)&nbsp;≤ 200 t / month  
   * Non-vegetable oils (Oil 1-3)&nbsp;≤ 250 t / month  
4. **Quality** – the hardness of the monthly blend must sit between **3 and 6**  
5. **Operational extras** (Williams “part 2” rules)  
   * Use **no more than 3 oils per month**  
   * If an oil is chosen, refine **at least 20 t** of it  
   * If *either* vegetable oil is used, **Oil 3 must also be used**

All refined tons sell immediately at **£150 / t**.  
Opening and closing stocks are fixed at 500 t for every oil.

---

## The Model

| Set | Description |
|-----|-------------|
| `i ∈ {Veg 1, Veg 2, Oil 1, Oil 2, Oil 3}` | Oil types |
| `j ∈ {Jan … Jun}` | Months |

| Variable | Units | Meaning |
|----------|-------|---------|
| `x[i,j] ≥ 0` | tons | Raw oil *i* purchased in month *j* |
| `s[i,j] ≥ 0` | tons | Start-of-month stock of oil *i* |
| `e[i,j] ≥ 0` | tons | End-of-month stock of oil *i* |
| `r[i,j] ≥ 0` | tons | Oil *i* refined (and sold) in month *j* |
| `b[i,j] ∈ {0,1}` | – | 1 ⇔ oil *i* is selected in month *j* |

### Objective
\[
\max \; \underbrace{150\sum_{i,j} r_{i,j}}_{\text{revenue}}
\;-\;
\underbrace{\sum_{i,j} c_{i,j}\,x_{i,j}}_{\text{buying}}
\;-\;
\underbrace{5\sum_{i,j} e_{i,j}}_{\text{holding}}
\]

### Key Constraints (high-level)
1. **Inventory flow** \(e_{i,j}=s_{i,j}+x_{i,j}-r_{i,j}\)  
2. **Capacity** \(\sum_{i\in\text{Veg}} r_{i,j}\le200,\;
                 \sum_{i\in\text{NonVeg}} r_{i,j}\le250\)  
3. **Hardness bounds** \(3\le \frac{\sum_i h_i r_{i,j}}{\sum_i r_{i,j}}\le6\)  
4. **“At most 3 oils”** \(\sum_i b_{i,j}\le3\)  
5. **Link `r`–`b`** \(20\,b_{i,j}\le r_{i,j}\le1000\,b_{i,j}\)  
6. **Veg ⇒ Oil 3** \(b_{\text{Veg 1},j}\le b_{\text{Oil 3},j}\),
   \(b_{\text{Veg 2},j}\le b_{\text{Oil 3},j}\)

---

## Repo Structure

.
├─ food_blending_gurobi.py   # full, commented model
└─ README.md                 # you’re reading it
