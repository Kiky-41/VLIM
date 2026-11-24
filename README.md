# VLIM

**Vectorized Lambda Iteration Method for Swift Economic Dispatch Analysis**

Rifki Rahman Nur Ikhsan · Raharjo, Jangkung · Rahmat, Basuki  
*Evergreen, 11(1), 435–447, 2024*  
[![DOI](https://img.shields.io/badge/DOI-10.5109%2F7172306-blue)](https://doi.org/10.5109/7172306)

Vectorized Lambda Iteration Method for fast economic dispatch.

## Abstract

The economic dispatch process in power markets aims to find the optimal generation schedule based on cost-based bids from generators. The lambda iteration method is commonly used but faces computational challenges for large-scale systems. To address this, a novel approach called the vectorized lambda iteration method is proposed. This method utilizes vectorization techniques to reduce computation time while maintaining accuracy. By parallelizing the computation of schedules and Lagrange multipliers, it achieves faster convergence and enables real-time decision-making. The method improves efficiency, competitiveness, and adaptability in power markets, ensuring reliable operation. Software and hardware optimizations further enhance performance. The results demonstrate a significant reduction in computation time and the lowest cost across three test systems. The vectorized lambda iteration method offers an efficient solution for power system operation and decision-making, with the potential for optimization in practical applications. The proposed vectorized variant that we introduced is capable of reducing computation time by 99% compared to conventional methods, while also achieving a 2% cost reduction.



## Proposed method

VLIM is the lambda iteration method rewritten with whole-array NumPy operations. For a trial $\lambda$, the dispatch of all units is one clipped vector expression, and $\lambda$ is corrected by the exact linearisation of the power-balance equation. The number of iterations therefore no longer grows with the number of units, and each iteration costs a few vector operations instead of a Python loop over generators. The same routine is applied hour by hour to the 42-unit Jamali load profile.

### Problem formulation

$$\min F=\sum_{i=1}^{N}\left(a_i+b_iP_i+c_iP_i^2\right)$$

subject to

$$\sum_{i=1}^{N}P_i=P_D,\qquad P_i^{\min}\le P_i\le P_i^{\max},\qquad -R_i^{D}\le P_i^{t}-P_i^{t-1}\le R_i^{U}.$$

### Notation

| Symbol | Meaning | Unit |
|---|---|---|
| $N$ | number of units | – |
| $\mathbf{P}$ | dispatch vector $[P_1,\dots,P_N]^\top$ | MW |
| $\mathbf{b},\mathbf{c}$ | vectors of linear and quadratic cost coefficients | USD/MWh, USD/MW²h |
| $\mathbf{P}^{\min},\mathbf{P}^{\max}$ | generation limit vectors | MW |
| $\mathbf{R}^{U},\mathbf{R}^{D}$ | ramp-up and ramp-down vectors | MW per hour |
| $P_D$ | demand of the current hour | MW |
| $\lambda$ | common incremental cost | USD/MWh |
| $\mathbf{1}$ | vector of ones | – |
| $t$ | hour index | – |

### Vectorised lambda iteration

Equal incremental cost gives the dispatch for the whole fleet at once:

$$\mathbf{P}(\lambda)=\mathrm{clip}\left(\frac{\lambda\mathbf{1}-\mathbf{b}}{2\mathbf{c}},\quad\mathbf{P}^{\min},\quad\mathbf{P}^{\max}\right).$$

The clip handles both limit cases: a unit below its minimum is fixed at $P_i^{\min}$, and a unit above its maximum is fixed at $P_i^{\max}$. Only the remaining units follow the linear rule.

To update $\lambda$, linearise the total generation around the current multiplier:

$$\sum_iP_i(\lambda_k+\Delta\lambda)\approx\sum_iP_i(\lambda_k)+\Delta\lambda\sum_i\frac{1}{2c_i}.$$

Requiring the left side to equal $P_D$ gives the correction

$$\lambda_{k+1}=\lambda_k+\frac{P_D-\sum_iP_i(\lambda_k)}{\sum_i\frac{1}{2c_i}}.$$

The denominator $\sum_i1/(2c_i)$ is the total sensitivity of generation to the multiplier, so the step is exact for the unconstrained units and converges quickly even when some units are clipped.

Ramp limits are applied as one masked vector operation against the previous hour:

$$\mathbf{P}\leftarrow\mathbf{P}^{t-1}+\mathrm{clip}\left(\mathbf{P}-\mathbf{P}^{t-1},\quad-\mathbf{R}^{D},\quad\mathbf{R}^{U}\right).$$

The clip keeps the change of each unit inside its ramp band; a positive difference is capped by the ramp-up limit and a negative difference by the ramp-down limit.

Iteration stops when $\left|P_D-\sum_iP_i\right|\le10^{-5}$ MW.

### Algorithm

1. Initialise $\lambda=\max_i b_i$ and $\mathbf P=\mathbf 0$.
2. Compute $\mathbf P(\lambda)$ with one clipped vector expression.
3. Apply the masked ramp projection for $t\gt1$.
4. Correct $\lambda$ with the balance linearisation and repeat from step 2.
5. Evaluate the cost and record unit schedules, ramp rates and limit violations.

### Implementation note

The notebook divides the reported cost by 1000, so the printed values are in thousands of the input currency unit. The README keeps the objective in the input unit.
### Reported result
The vectorised loop replaces the per-unit scan, so the 42-unit Jamali 24-hour schedule is produced with a handful of global iterations per hour. The paper reports the computation time of this implementation against the conventional loop-based lambda iteration on the same dataset.

## Contents

```
.gitignore
VLIM.ipynb
```

## Data

Input data is **not included**. Place files in `data/` (notebooks) or next to the `.m` script (MATLAB):

| File | Columns |
|---|---|
| `input42u.xlsx` | Sheet1: `Unit`, `a`, `b`, `c`, `pmin`, `pmax`, `rampup`, `rampdown`, `load` |
| `power_demand42u.xlsx` | Sheet1: `load` |


## Results

| Metric | Value |
|---|---|
| Test case | 42-unit Jamali, 24 hours |
| Solver | vectorised $\lambda$ iteration |
| Per-hour work | one clipped vector expression plus one multiplier correction |
| Reference | computation time compared against loop-based lambda iteration |


## Requirements

Python. Python notebooks: `numpy`, `pandas`, `scipy`, `matplotlib` (as imported). MATLAB scripts: R2021a+.

## Citation

```bibtex
@article{Rifki_Rahman_Nur_Ikhsan_2024, title={Vectorized Lambda Iteration Method for Swift Economic Dispatch Analysis}, volume={11}, ISSN={2432-5953}, url={http://dx.doi.org/10.5109/7172306}, DOI={10.5109/7172306}, number={1}, journal={Evergreen}, publisher={Kyushu University}, author={Rifki Rahman Nur Ikhsan and Raharjo, Jangkung and Rahmat, Basuki}, year={2024}, month=Mar, pages={435–447} }
```

## License

[![CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](LICENSE)

Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). **Attribution is required**: any use of this code, results, or figures, including in publications, must cite:

> Rifki Rahman Nur Ikhsan; Jangkung Raharjo; Basuki Rahmat (2024). Vectorized Lambda Iteration Method for Swift Economic Dispatch Analysis. *Evergreen*. https://doi.org/10.5109/7172306
