# Research Proposal: MAB-Based Adaptive Sparsification for Federated Learning

**Target venue:** IEEE GLOBECOM 2026  
**Timeline:** 19 days to submission  
**Supervisor:** Prof. Ning Lu (Queen's University)

---

## 1. Title (working)

**Learning to Sparsify: Multi-Armed Bandit for Adaptive Communication-Efficient Federated Learning**

*(Alternative: Adaptive Sparsification Rates for Federated Learning via Multi-Armed Bandits)*

---

## 2. Abstract / Summary

Federated learning (FL) requires frequent exchange of model updates over resource-constrained and heterogeneous networks. DSFL (Dynamic Sparsification for Federated Learning) reduces communication via two-level sparsification—layer-wise similarity sparsification (LSS) and per-client top-*K*—but assumes that the sparsification rate *K* is given by a *fixed* capacity model (e.g. sampled from a truncated normal). We propose to replace this static policy with a **multi-armed bandit (MAB)** that **learns client-specific sparsification rates online**, adapting to dynamic wireless conditions. Our contribution is a bandit-based extension of DSFL that (i) selects *K* per client per round from a finite set of compression levels, (ii) uses reward feedback (e.g. loss improvement vs. communication cost) to balance exploration and exploitation, and (iii) provides a clear differentiation from prior work: Han et al. use online learning without bandit/regret; Jiang et al. use MAB for *pruning* ratio; we use MAB for *communication* sparsification rate with DSFL’s layer-aware (LSS) updates. We target GLOBECOM 2026 with a 6-page submission, then extend to a journal (e.g. TNNLS/ToN) with full theory and more benchmarks.

---

## 3. Motivation and Context

### 3.1 Lineage

- **DSFL** (Beitollahi et al., ICCSPA 2022): Two-level sparsification (LSS + top-*K*), error accumulation; *K* = *C*_i(*t*) sampled from a truncated normal—**no learning**.
- **FLAC** (Beitollahi et al., GLOBECOM 2022): Autoencoder compression + convergence guarantee; different compression mechanism.
- **After Queen’s:** No follow-up on DSFL in the lab; Mahdi pivoted to foundation models / federated unlearning (Huawei). Prof. Lu’s FL publication gap is 2+ years; this project **restarts the FL compression thread** with an adaptive learning angle.

### 3.2 What DSFL Left Unfinished (stated in the paper)

1. CKA similarity is computationally expensive on devices—no solution.
2. LSS uses the *previous* round’s global model—delayed approximation.
3. No client scheduling—all clients participate every round.
4. **No MAB component—*K* is from a fixed distribution, not learned.** ← **Our focus.**

### 3.3 One-Sentence Angle

> **DSFL assumes heterogeneous but static policy for sparsification rate; we introduce a MAB framework that learns client-specific sparsification rates online, adapting to dynamic wireless conditions.**

---

## 4. Related Work and Differentiation

### 4.1 What Exists (must cite and differentiate)

| Prior work | What it does | Our differentiation |
|------------|--------------|---------------------|
| **Han et al. (ICDCS 2020)** | Adaptive gradient sparsification via **online learning**; non-IID. | No MAB → no regret bounds, no exploration–exploitation; we add bandit + (sublinear) regret. |
| **Jiang et al. (FedMP)** | MAB for **pruning ratio** (which weights to remove from model). | Pruning ≠ sparsification; they do *computation* efficiency, we do *communication* sparsification rate; we keep DSFL’s LSS (layer-aware). |
| **Xia et al. (TWC 2020, CS-UCB)** | MAB for **client scheduling** (who to select). | They choose *who*; we choose *how much* to compress (K) per client. |
| **FedCG** | Client selection + gradient compression; heuristics. | No MAB, no regret guarantees. |
| **Vaishnav et al. (IoTJ 2024)** | Communication-adaptive gradient sparsification + error compensation. | Check if MAB or heuristic; we add explicit MAB for K and layer-aware (LSS) setting. |
| **Vaishnav et al. (Budgeted UCB, 2025)** | Budgeted UCB for IoT; time-varying bandwidth; sublinear regret. | **Theoretical template** for our UCB under capacity/bandwidth constraints. |

### 4.2 Confirmed Gap (contribution space)

| Exists | Missing (our contribution) |
|--------|----------------------------|
| MAB for client *selection* | MAB for sparsification *rate* per client |
| Fixed / sampled K (DSFL) | Learned, adaptive K per client per round |
| Adaptive sparsification via online learning (no regret) | UCB-based adaptive sparsification with regret guarantees |
| MAB for *pruning* ratio (Jiang) | MAB for *model-difference* sparsification rate + LSS |
| Static capacity sampling (DSFL) | Dynamic channel–aware bandit |

---

## 5. Objectives

1. **Design** a MAB formulation for selecting sparsification rate *K* (or a discrete set of K-levels) per client per round, compatible with DSFL’s LSS + top-*K* pipeline.
2. **Implement** the algorithm on top of DSFL (e.g. Mahdi’s GitHub codebase) and run **dynamic capacity** experiments so the bandit has something to adapt to.
3. **Show** gains over DSFL (fixed policy), fixed top-*K*, and Han et al. adaptive GS; optionally plot regret or convergence vs. communication cost.
4. **Submit** a 6-page paper to GLOBECOM 2026 (Phase 1), then extend to a journal with full theory and more benchmarks (Phase 2).

---

## 6. Method Outline

### 6.1 System model (high level)

- Same as DSFL: server, *N* clients, global model *w*[*t*], local updates Δ*w*_i[*t*], weighted aggregation.
- **Addition:** Per round *t*, for each client *i*, an **action** is chosen (e.g. *K*_i ∈ {*K*_min, …, *K*_max} or a small discrete set). This *K*_i is used in DSFL’s top-*K* step instead of sampling from a distribution.
- **Reward:** Define a scalar reward (e.g. negative loss change minus communication cost, or loss improvement only) so the bandit can learn which *K* works well under current conditions.

### 6.2 Algorithm sketch

- **Bandit:** UCB-style (or Budgeted UCB if we enforce capacity constraints). Arms = compression levels (or (client, level) if we do per-client arms).
- **Integration:** Each round: (1) Bandit selects *K*_i for each client *i*; (2) Run DSFL’s LSS then top-*K*_i; (3) Observe reward; (4) Update bandit statistics.
- **Theory (GLOBECOM):** At least one of: (a) convergence guarantee under bounded variance + our policy, or (b) sublinear regret claim with reference to existing bandit analysis (e.g. Steiger, Vaishnav). Full proof can go in appendix or journal version.

### 6.3 Baselines (experiments)

- DSFL (original, K from truncated normal).
- Fixed top-*K* (same K for all).
- Han et al. adaptive gradient sparsification (ICDCS 2020).
- Random K assignment.
- FedAvg with no compression (upper bound on accuracy).

---

## 7. Evaluation Plan

### 7.1 Datasets and settings

- **Minimum for GLOBECOM:** MNIST + CIFAR-10 (or MNIST + FEMNIST); non-IID (e.g. Dirichlet α or label skew); 10–20 clients.
- **Dynamic capacity:** Time-varying *C*_i(*t*) (e.g. Rayleigh fading–based or Markovian) so that fixed K policy is suboptimal and the bandit can show benefit.
- **Metrics:** Test accuracy vs. communication round; accuracy vs. total bits transmitted; optionally cumulative regret.

### 7.2 Experiments to run

1. **Main result:** Convergence / accuracy vs. rounds (and vs. bits) under dynamic capacity: our MAB-DSFL vs. DSFL vs. fixed K vs. Han et al.
2. **Ablation:** Effect of number of arms (K-levels); effect of reward design.
3. **Non-IID sweep:** Vary α (Dirichlet) to show robustness.
4. **Regret curve (if space):** Cumulative regret over rounds to show sublinear growth.

---

## 8. Theoretical Must-Haves

- **GLOBECOM (6 pages):** At least one of: (i) convergence bound (possibly “under standard assumptions, convergence follows from …” + citation), or (ii) regret bound / sublinear regret with reference to prior bandit result. Algorithm description and intuition must be clear.
- **Journal extension:** Full convergence analysis; full regret analysis; possibly client scheduling + MAB for K together.

---

## 9. Urgent Reading (before locking the write-up)

1. **Jiang et al. (FedMP)** — MAB for pruning ratio; sharpen “pruning vs. sparsification + LSS” differentiation.
2. **Han et al. (ICDCS 2020)** — adaptive GS; sharpen “online learning vs. MAB + regret.”
3. **Vaishnav et al. (IoTJ 2024)** — communication-adaptive sparsification; check if MAB used.
4. **Xia et al. (TWC 2020, CS-UCB)** — MAB for scheduling; reference for UCB and regret.
5. **Vaishnav Budgeted UCB (2025)** — template for bandwidth/capacity constraints.

---

## 10. Phase 1: GLOBECOM 2026 (19 days)

**Goal:** Submit a complete, coherent 6-page paper by the deadline.

| Window | Tasks |
|--------|--------|
| **Days 1–3** | Finalize system model and bandit formulation (actions, reward, integration with DSFL). Skim Han + Jiang; write related-work paragraph. |
| **Days 4–8** | Implementation: plug MAB into DSFL codebase; implement dynamic capacity (e.g. time-varying C_i(t)); run DSFL + fixed K + your MAB on MNIST. |
| **Days 9–12** | Add CIFAR-10 (or FEMNIST); run Han et al. baseline if code available (or cite and compare to their reported results); generate main figures (accuracy vs. rounds, accuracy vs. bits). |
| **Days 13–16** | Write full draft: intro, related work, system model, algorithm, experiments, conclusion. Insert one convergence or regret subsection (with reference if needed). |
| **Days 17–19** | Polish, internal review, format to IEEE template, submit. |

**Deliverable:** One 6-page GLOBECOM submission (PDF + source/experiments if required).

---

## 11. Phase 2: Second Step (after GLOBECOM)

**Goal:** Turn the GLOBECOM contribution into a stronger, journal-ready story and/or a follow-up conference paper.

### 11.1 Option A — Journal extension (TNNLS / ToN)

- **Content:** Full convergence proof for MAB-DSFL; full regret analysis (or detailed reduction to existing bandit result); more datasets (e.g. FEMNIST, Speech, UCI HAR); non-IID sweep; regret curves; ablation studies.
- **Timeline:** Start after submission; aim for journal submission within 2–4 months of GLOBECOM notification (or after revision if GLOBECOM is accepted).

### 11.2 Option B — Client scheduling + MAB for K (INFOCOM 2027)

- **Content:** Combine MAB for *client selection* (who participates) with MAB for *sparsification rate* (K). Two bandits or one joint formulation; 9-page format; tighter theory.
- **Timeline:** INFOCOM 2027 deadline (e.g. July 2026); build on GLOBECOM draft and Steiger’s bandit work in the lab.

### 11.3 Option C — Lighter CKA / LSS (future work)

- **Content:** Address DSFL’s “CKA expensive” and “LSS delayed” limitations (e.g. cheaper similarity proxy, or one-round-delay analysis). Can be a separate section in the journal version or a follow-up paper.

**Recommended second step:** **Option A (journal extension)** as the default; Option B if you want a second conference target and have bandwidth.

---

## 12. Summary Table

| Item | Detail |
|------|--------|
| **Title (working)** | Learning to Sparsify: MAB for Adaptive Communication-Efficient FL |
| **Venue** | IEEE GLOBECOM 2026 (6 pages) |
| **Deadline** | ~19 days from now |
| **Core idea** | Replace DSFL’s fixed K-policy with a MAB that learns K per client per round |
| **Baselines** | DSFL, fixed top-K, Han et al., random K, FedAvg (no comp.) |
| **Experiments** | MNIST + CIFAR-10 (or FEMNIST), dynamic capacity, non-IID |
| **Theory** | Convergence and/or regret (with reference ok for 6 pages) |
| **Second step** | Journal extension (TNNLS/ToN) or INFOCOM 2027 (client selection + K) |

---

*Document version: Research proposal derived from dsfl-update notes; Phase 1 (GLOBECOM) and Phase 2 (journal / INFOCOM) defined.*
