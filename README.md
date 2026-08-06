# SD-TRK: Numerical Experiments

Reproducible code for the numerical experiments (Section 5) of

> **A Spectrally Damped Tensor Randomized Kaczmarz Method for Doubly Noisy Tensor Systems**
> Nahyun Lee\*, Jiyoung Choi\* — Department of Mathematics, University of California San Diego
>
> [![arXiv](https://img.shields.io/badge/arXiv-2607.13552-b31b1b.svg)](https://arxiv.org/abs/2607.13552)
> **Status:** Under review for publication; submitted to *BIT Numerical Mathematics*.

---

## What this repository contains

Tensor randomized Kaczmarz (TRK) methods are row-action solvers for tensor linear systems `A * X = B` under the t-product. The paper studies them under a **doubly noisy** model, in which both the system tensor and the right-hand side are corrupted,

```
Ã = A + δ_A·G_A,        B̃ = B + δ_B·G_B,
```

and introduces **SD-TRK**, a spectrally damped variant whose update is applied slice-wise in the Fourier domain with frequency-dependent damping parameters `λ⁽ᵏ⁾`. The two behaviors the experiments are built to expose are:

- **semi-convergence** of standard TRK — the true error `‖X^t − X*‖_F` falls, then reverses and grows, because the undamped update repeatedly inverts near-singular spectral components.
- **stabilization** by SD-TRK — the damping acts as a spectral regularizer, so the true error decreases nearly monotonically to an error floor.

Each notebook is **self-contained**: it defines its own t-product helpers (`t_prod`, `t_transpose`, `t_inv`, `tnorm`), the TRK / SD-TRK update steps, the data generator, the plotting code, and a markdown header stating the setup and the expected numbers. There is no shared module to install and no hidden state between notebooks — open one, run it top to bottom, and it writes its figure.

---

## Figure map

| Notebook | Paper | Figure | Output | What it shows |
|---|---|---|---|---|
| [`Figure_1.ipynb`](Figure_1.ipynb) | §5.1.1–5.1.2 | Fig. 1 | `Fig1a.eps`, `Fig1b.eps` | **Baseline.** (a) Standard TRK converges linearly on a noiseless consistent system. (b) Under doubly noisy perturbation (`δ ∈ {0, 1e-4, 1e-2}`) the error decays and then stalls at a nonzero level — the motivating failure mode. |
| [`Figure_2.ipynb`](Figure_2.ipynb) | §5.1.3 | Fig. 2 | `Fig2a.eps`, `Fig2b.eps` | **Relatively well-conditioned comparison.** Both methods are stable; SD-TRK is better on all three metrics. The real point is *diagnostic*: for standard TRK the observable residual plateaus long before the true error does, so residual-based stopping is unreliable; for SD-TRK the two stabilize together. |
| [`Figure_3.ipynb`](Figure_3.ipynb) | §5.1.4 | Fig. 3 | `Fig3a.eps`, `Fig3b.eps` | **Ill-conditioned comparison.** Lateral slices are scaled geometrically down to `2e-2` to force small singular values. Standard TRK traces the classic V-shaped semi-convergence curve up to ≈ `12.1125`; SD-TRK settles near ≈ `2.1013` (≈ 5.76× smaller). |
| [`Figure_4.ipynb`](Figure_4.ipynb) | §5.1.5 | Fig. 4 | `Fig4.eps` | **Paired sparse-target test.** Both methods see the same `X*`, the same `Ã`, `B̃`, *and the same row-sampling sequence* in each trial, so the gap cannot be blamed on sampling luck. Sparse target, ill-conditioned: `10.9761` vs. `2.9251` (≈ 3.75×). |
| [`Figure_5.ipynb`](Figure_5.ipynb) | §5.2.2 | Fig. 5 | `Fig5.pdf` | **Application.** Two-pass (row, then column) deblurring of a `256×170` photo of Geisel Library (tensor size `170×256×3`). Reports relative error and PSNR: degraded `0.1289 / 22.5856 dB`, standard TRK `0.1246 / 22.8824 dB`, SD-TRK `0.1025 / 24.5723 dB`. |
| [`Figure_6.ipynb`](Figure_6.ipynb) | §5.2.2 | Fig. 6 | `Fig6.eps` | **Same run as Fig. 5**, with residual histories recorded. Tracks the row- and column-pass residuals for both methods; SD-TRK reaches a lower final residual in the row pass. |

Notebook numbering matches the figure numbering of the manuscript.

---

## Experimental settings

All synthetic experiments use dimensions `(n₁, n₂, n₃, n₄) = (20, 10, 5, 5)`, start from `X⁰ = 0`, and use `λ_floor = 1e-6` in the slice-dependent damping rule.

| Notebook | Noise | SD-TRK params | Iterations × trials | Seed |
|---|---|---|---|---|
| Figure 1 | `δ_A = δ_B ∈ {0, 1e-4, 1e-2}` | — | 1000 × 1 per level | `42` |
| Figure 2 | `δ_A = 1e-3`, `δ_B = 0.5` | `ω = 0.9`, `λ_scale = 5.0` | 2000 × 20 | `7` |
| Figure 3 | `δ_A = 1e-2`, `δ_B = 0.5` | `ω = 0.7`, `λ_scale = 100.0` | 2000 × 20 | `7` |
| Figure 4 | `δ_A = 1e-2`, `δ_B = 0.5` | `ω = 0.8`, `λ_scale = 50.0` | 2000 × 10 paired | `25` |
| Figure 5–6 | `δ_A = 1e-4`, `δ_B = 1e-2` | `ω = 0.30`, `λ = 1e-2` (scalar) | 1200 per pass | `41` |

The standard TRK baseline in Figures 2–6 is the same solver with `ω = 1`, `λ = 0`.

---

## Running

Requirements: Python 3.13.3, `numpy`, `matplotlib`, and `pillow` (Figures 6–7 only, for loading `geisel.jpg`).

```bash
pip install numpy matplotlib pillow jupyter
jupyter notebook
```

Each notebook writes its figure at final print size (8 pt lettering, TrueType fonts
embedded) in **EPS**, except the image-reconstruction panel, which is a photographic
figure and is written as a 600 dpi **PDF**. Output file names match the figure
numbering above (`Fig1a.eps`, `Fig1b.eps`, …, `Fig5.pdf`, `Fig6.eps`).

Reported results were produced on a MacBook Pro (Apple M3 Pro, 12-core CPU, 18 GB unified memory), macOS, Python 3.13.3.

---

## Citation

```bibtex
@misc{lee_choi_sdtrk,
  title         = {A Spectrally Damped Tensor Randomized Kaczmarz Method for Doubly Noisy Tensor Systems},
  author        = {Lee, Nahyun and Choi, Jiyoung},
  year          = {2026},
  eprint        = {2607.13552},
  archivePrefix = {arXiv},
  primaryClass  = {math.NA},
  note          = {Submitted to BIT Numerical Mathematics}
}
```
