# VLIM

**Vectorized Lambda Iteration Method for Swift Economic Dispatch Analysis**

Rifki Rahman Nur Ikhsan · Raharjo, Jangkung · Rahmat, Basuki  
*Evergreen, 11(1), 435–447, 2024*  
[![DOI](https://img.shields.io/badge/DOI-10.5109%2F7172306-blue)](https://doi.org/10.5109/7172306)

Vectorized Lambda Iteration Method for fast economic dispatch.

## Abstract

The economic dispatch process in power markets aims to find the optimal generation schedule based on cost-based bids from generators. The lambda iteration method is commonly used but faces computational challenges for large-scale systems. To address this, a novel approach called the vectorized lambda iteration method is proposed. This method utilizes vectorization techniques to reduce computation time while maintaining accuracy. By parallelizing the computation of schedules and Lagrange multipliers, it achieves faster convergence and enables real-time decision-making. The method improves efficiency, competitiveness, and adaptability in power markets, ensuring reliable operation. Software and hardware optimizations further enhance performance. The results demonstrate a significant reduction in computation time and the lowest cost across three test systems. The vectorized lambda iteration method offers an efficient solution for power system operation and decision-making, with the potential for optimization in practical applications. The proposed vectorized variant that we introduced is capable of reducing computation time by 99% compared to conventional methods, while also achieving a 2% cost reduction.


## Proposed method

VLIM is the lambda iteration method rewritten with whole-array NumPy operations. Instead of looping over units, the dispatch for a trial $\lambda$ is one clipped vector expression, and $\lambda$ is corrected by the exact linearisation of the power-balance equation, so the number of iterations no longer scales with the unit count.

### Problem formulation

$$\min F=\sum_{i=1}^{N}\left(a_i+b_iP_i+c_iP_i^2\right)$$

subject to

$$\sum_{i=1}^{N}P_i=P_D,\qquad P_i^{\min}\le P_i\le P_i^{\max},\qquad -R_i^{D}\le P_i^{t}-P_i^{t-1}\le R_i^{U}$$

### Vectorised lambda iteration

Equal incremental cost gives, for the whole fleet at once,

$$\mathbf{P}(\lambda)=\operatorname{clip}\!\left(\frac{\lambda\mathbf{1}-\mathbf{b}}{2\mathbf{c}},\;\mathbf{P}^{\min},\;\mathbf{P}^{\max}\right)$$

with $\mathbf 1$ a vector of ones. Linearising $\sum_iP_i(\lambda)$ around $\lambda_k$:

$$\sum_iP_i(\lambda_k+\Delta\lambda)\approx\sum_iP_i(\lambda_k)+\Delta\lambda\sum_i\frac{1}{2c_i}$$

and requiring the left side to equal $P_D$ gives the vectorised multiplier update

$$\lambda_{k+1}=\lambda_k+\frac{P_D-\sum_iP_i(\lambda_k)}{\sum_i\frac{1}{2c_i}}$$

Ramp limits are applied as one masked vector operation against the previous hour:

$$\mathbf{P}\leftarrow\mathbf{P}^{t-1}+\operatorname{clip}\!\left(\mathbf{P}-\mathbf{P}^{t-1},\;-\mathbf{R}^{D},\;\mathbf{R}^{U}\right)$$

Iteration stops when $\left|P_D-\sum_iP_i\right|\le10^{-5}$ MW.

### Algorithm

1. Initialise $\lambda=\max_i b_i$, $\mathbf P=\mathbf 0$.
2. Compute $\mathbf P(\lambda)$ with one clipped vector expression.
3. Apply the masked ramp projection for $t>1$.
4. Correct $\lambda$ with the balance linearisation; repeat from step 2.
5. Evaluate the cost and record unit schedules, ramp rates and limit violations.

The 42-unit Jamali 24-hour case is solved hour by hour with this loop; the reported implementation is the reference for the computation-time comparison in the paper.

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
