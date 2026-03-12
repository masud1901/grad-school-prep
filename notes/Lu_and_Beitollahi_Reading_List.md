# Ning Lu & Mehdi Beitollahi — Aligned Reading List

**Goal:** Balance **Ning Lu's** wireless/scheduling/compression work with **Mehdi Beitollahi's** FL work. Lu papers are systems/networking + bandits; Mehdi's Lu-lab papers are communication-efficient FL. Your proposal extends **POSS**, **FLAC**, and bandit scheduling — so Lu papers should be priority.

---

## Part A: Ning Lu Papers (wireless, bandits, FL systems)

These are **directly aligned** with Prof. Lu's lab: scheduling, queueing, compression, convergence under communication constraints.

| # | Citation | Venue | Why read |
|---|----------|--------|----------|
| **Lu-1** | **Steiger, Li, Ji, Lu.** "Constrained Bandit Learning with Switching Costs for Wireless Networks." | IEEE INFOCOM 2023 | **POSS** — core bandit framework your proposal extends (robust mean estimator, heavy tails). |
| **Lu-2** | **Steiger, Li, Lu.** "Backlogged Bandits: Cost-Effective Learning for Utility Maximization in Queueing Networks." | IEEE INFOCOM 2024 | Extends bandit scheduling to queueing; connects to wireless/scheduling. |
| **Lu-3** | **Steiger, Li, Lu.** "Learning from Delayed Semi-Bandit Feedback under Strong Fairness Guarantees." | IEEE INFOCOM 2022 | Delayed feedback + fairness — same team, same style; good for “math tools” in Lu’s bandit work. |
| **Lu-4** | **Beitollahi, Lu.** "FLAC: Federated Learning with Autoencoder Compression and Convergence Guarantee." | IEEE GLOBECOM 2022 | **Lu + Mehdi.** Compression via autoencoder; **convergence proof** (strongly convex, non-IID). Your proposal couples scheduling with FLAC. **Conference paper (short).** |
| **Lu-5** | **Beitollahi, Liu, Lu.** "DSFL: Dynamic Sparsification for Federated Learning." | ICCSPA 2022 | **Lu + Mehdi.** Dynamic sparsification per client; communication-efficient FL. **Conference paper (short).** |
| **Lu-6** | **Beitollahi, Lu.** "Federated Learning over Wireless Networks: Challenges and Solutions." | IEEE Internet of Things J. 2023 | ✅ **Done** — review/survey (paper 01); no notes. |

**Where to get them:** IEEE Xplore (FLAC, INFOCOM), Queen’s QSpace (Lu’s group may have preprints). **Steiger papers** (Lu-1, Lu-2, Lu-3): store PDFs in `steiger papers/`. Search: “Ning Lu Queen's” or “Steiger Lu” + paper title.

---

## Part B: Mehdi Beitollahi — Lu-lab vs later work

### B1 — Co-authored with Ning Lu (Lu lab; read with Part A)

- **FLAC** → see Lu-4 above — **conference paper (short).**
- **DSFL** → see Lu-5 above — **conference paper (short).**
- **IoTJ survey** → see Lu-6 above ✅ **Done** (review; no notes)

### B2 — Mehdi’s later work (Noah’s Ark / other; pure FL / ML)

What you already have in `mehdi papers/`:

| Your # | Paper | Venue | Note |
|--------|--------|--------|------|
| 02 | Layer Normalization in Label-Skewed FL (Zhang, **Beitollahi**, Bie, Chen) | TMLR 2024 | ✅ Done — notes in `02_Layer_Normalization_Label_Shift_FL.md` |
| 04 | One-Shot FL with Foundation Models (FedPFT) | TMLR 2025 | Mehdi; foundation models + FL |
| 05 | Federated Unlearning via Weight Negation (NoT) | CVPR 2025 | Mehdi; unlearning |
| 06 | Domain Generalization with Latent Diffusion | ICML 2024 workshop / TMLR 2024 | Mehdi; DG, not FL |
| 07 | Combining LoRA Modules for Few-Shot Transfer | ICML 2024 workshop | Mehdi; LoRA, not FL |

These are **math-heavy, pure FL/ML**. Keep them for breadth, but for **Ning Lu alignment** prioritize **Part A** first.

---

## Suggested reading order (Lu-aligned)

1. **FLAC** (Lu-4) — Lu + Mehdi; compression + convergence; directly in your proposal.  
2. **POSS** (Lu-1) — Constrained bandits with switching costs; you extend this.  
3. **DSFL** (Lu-5) — Lu + Mehdi; dynamic sparsification.  
4. **Backlogged Bandits** (Lu-2) — Queueing + bandits.  
5. **Delayed Semi-Bandit** (Lu-3) — Same team; fairness + delayed feedback.  
6. Then Mehdi’s FedPFT / NoT / etc. as you want more FL depth.

---

## Quick refs (links)

- **FLAC:** [IEEE Xplore](https://ieeexplore.ieee.org/document/10000743/)  
- **DSFL:** [IEEE Xplore](https://ieeexplore.ieee.org/document/10019204/)  
- **POSS (INFOCOM 2023):** Search “Constrained Bandit Learning with Switching Costs Steiger Lu” on IEEE Xplore.  
- **Backlogged Bandits (INFOCOM 2024):** [IEEE Xplore](https://ieeexplore.ieee.org/document/10621295/)  
- **Mehdi’s page (papers + links):** [mahdibeit.github.io](https://mahdibeit.github.io/)  
- **Lu’s lab / Queen’s ECE:** [Ning Lu - Queen's ECE](https://www.ece.queensu.ca/people/N-Lu/)

---

*Add notes under `notes/` as you go (e.g. `03_FLAC.md`, `04_POSS.md`).*
