# VLIM

**Vectorized Lambda Iteration Method for Swift Economic Dispatch Analysis**

Rifki Rahman Nur Ikhsan · Raharjo, Jangkung · Rahmat, Basuki  
*Evergreen, 11(1), 435–447, 2024*  
[![DOI](https://img.shields.io/badge/DOI-10.5109%2F7172306-blue)](https://doi.org/10.5109/7172306)

Vectorized Lambda Iteration Method for fast economic dispatch.

## Abstract

The economic dispatch process in power markets aims to find the optimal generation schedule based on cost-based bids from generators. The lambda iteration method is commonly used but faces computational challenges for large-scale systems. To address this, a novel approach called the vectorized lambda iteration method is proposed. This method utilizes vectorization techniques to reduce computation time while maintaining accuracy. By parallelizing the computation of schedules and Lagrange multipliers, it achieves faster convergence and enables real-time decision-making. The method improves efficiency, competitiveness, and adaptability in power markets, ensuring reliable operation. Software and hardware optimizations further enhance performance. The results demonstrate a significant reduction in computation time and the lowest cost across three test systems. The vectorized lambda iteration method offers an efficient solution for power system operation and decision-making, with the potential for optimization in practical applications. The proposed vectorized variant that we introduced is capable of reducing computation time by 99% compared to conventional methods, while also achieving a 2% cost reduction.

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
