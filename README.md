# Fast Distributed NMPC by Exploiting the Parametric Structure of the Local Problems

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXXXX)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Companion code for the paper

> "Fast Distributed Nonlinear Model Predictive Control by Exploiting the
> Parametric Structure of the Local Problems," 

## Idea

In distributed NMPC based on consensus ADMM, each agent solves its local nonlinear program (NLP)
in every communication round, although only the coordination variables (the consensus variable
and the dual variables) change from one round to the next. We treat these coordination variables
as **parameters** of the local NLP. They enter the KKT conditions affinely, so the KKT matrix does
not depend on them. Most exact local solves can therefore be replaced by **a single Newton step on
the active-set KKT conditions** (a sensitivity update), which costs one linear solve.

Exact local solves are only used

1. in the first round of each sample, when the measured state changes;
2. while the ADMM residuals are above a threshold;
3. when an acceptance test detects a change of the active set.

The coordination algorithm itself is unchanged: the agents exchange the same information as in
standard ADMM-based distributed NMPC.

## Contents

| File | Description |
|---|---|
| `sens_dmpc_quadrotors.ipynb` | Simulation studies of the paper (figures and tables) |
| `sens_dmpc_quadrotors_realtime.ipynb` | The same studies with the ADMM iterations stopped at the sampling time |
| `figures/` | Result figures and animations |
| `requirements.txt` | Python dependencies |

Both notebooks consider three quadrotors (12 states, 4 inputs each) that must keep a minimum
distance of 1 m from each other, in two scenarios:

1. **Point-to-point:** the quadrotors swap sides through a common region.
2. **Tracking:** the quadrotors follow three intersecting elliptical orbits.

Three controllers are compared: centralized NMPC, standard distributed NMPC (exact local solves
with IPOPT in every round) and the proposed sensitivity-assisted distributed NMPC.

## Installation

Python 3.10 or newer is required.

```bash
git clone https://github.com/<user>/<repository>.git
cd <repository>
pip install -r requirements.txt
```

The local problems are formulated with the CasADi `Opti` stack and solved with IPOPT, which is
included in the CasADi package. The results in the paper were obtained with CasADi 3.7.2.


## Usage

```bash
jupyter notebook sens_dmpc_quadrotors.ipynb            # results of the paper
jupyter notebook sens_dmpc_quadrotors_realtime.ipynb   # ADMM stopped at the sampling time
```

## Results

Point-to-point scenario:

![figures/fig_closedloop.png](figures/fig_closedloop.png)

Tracking scenario:

![](figures/fig_tracking.png)

Panels: (a) closed-loop paths, (b) distance of the closest pair, (c) average tracking error,
(d) online computation time per sample, (e) local updates per sample in the proposed method.

Summary of the two distributed schemes (single core of an Apple M4 Pro, CasADi 3.7.2):

| | Point-to-point: standard | Point-to-point: proposed | Tracking: standard | Tracking: proposed |
|---|:---:|:---:|:---:|:---:|
| computation per sample, mean / max [ms] | 46.1 / 161.9 | 10.7 / 40.3 | 119.0 / 245.1 | 34.3 / 116.8 |
| samples with computation > T<sub>s</sub> = 100 ms | 12 % | 0 % | 60 % | 4 % |
| exact local solves | 1557 | 152 | 5850 | 424 |
| sensitivity updates (rejected) | – | 1405 (2) | – | 5426 (184) |
| max. deviation of the closed-loop trajectories [m] | | 1.4·10⁻⁵ | | 5.9·10⁻⁵ |

The proposed method yields the same closed-loop trajectories as standard distributed NMPC, while
reducing the computation time by a factor of 3.5–4.3.



## Citation

If you use this code, please cite the paper above and the archived software:

```bibtex
@software{krishnamoorthy2026code,
  author    = {Krishnamoorthy, Dinesh},
  title     = {Fast distributed {NMPC} by exploiting the parametric structure of the local problems: companion code},
  year      = {2026},
  publisher = {Zenodo},
  version   = {1.0.0},
  doi       = {10.5281/zenodo.XXXXXXX},
  url       = {https://doi.org/10.5281/zenodo.XXXXXXX}
}
```

## License

This code is released under the MIT License, see [LICENSE](LICENSE). It uses
[CasADi](https://web.casadi.org) (LGPL-3.0) and [IPOPT](https://github.com/coin-or/Ipopt)
(EPL-2.0), which are installed separately as dependencies and keep their own licenses.

## Contact

Dinesh Krishnamoorthy, Norwegian University of Science and Technology (NTNU),
dinesh.krishnamoorthy@ntnu.no
