# Dynamic Accurate User Selection for Autoencoder-Compressed Federated Learning

**Type:** Research Proposal — Paper 2 of 3  
**Working Title:** DynAC-FLAC: Loss-Driven Accurate User Selection for Communication-Efficient Federated Learning  
**Target Venue:** IEEE INFOCOM 2027 (A*) or IEEE Transactions on Networking (Journal)  
**Deadline:** INFOCOM 2027 — July 24, 2026 | Journal — rolling  
**Timeline:** ~30 days (April–May 2026, after ADAFL submission)  
**Author:** Md Akmol Masud — Incoming M.A.Sc., ECE, Queen's University (September 2026)  
**Supervisor:** Prof. Ning Lu (Canada Research Chair, Tier 2, Future Communication Networks)  
**Prerequisite:** ADAFL (Paper 1, Globecom 2026) — this proposal builds directly on it

---

## 1. Executive Summary

FLAC (Federated Learning with Autoencoder Compression, Beitollahi & Lu, Globecom 2022) is the most theoretically complete FL compression paper from Prof. Lu's lab. It trains a server-side autoencoder to encode and decode client model updates, achieves compression rates from 83x to 875x, and proves O(1/T) convergence for non-i.i.d. data. However, it has one critical structural weakness that has never been addressed in the literature: the **"accurate users" (AC) subset** — the clients who send uncompressed updates to help the autoencoder reconstruct everyone else — is **selected arbitrarily and held static throughout training**.

We propose **DynAC-FLAC**: a dynamic, loss-feedback driven policy for selecting which clients join the AC subset each round, using the same ΔL_i[t] training signal introduced in ADAFL (Paper 1). Clients whose updates the autoencoder reconstructs poorly, and whose local loss is improving rapidly, are prioritized for AC inclusion — because they carry the most unique, informative updates. Clients well-represented by the autoencoder are compressed normally. This policy is principled, zero-overhead (uses signals already computed in FLAC's pipeline), and provably preserves FLAC's existing O(1/T) convergence guarantee under mild conditions.

This is not a standalone idea — it is the natural extension of ADAFL's ΔL feedback signal from DSFL's sparsification pipeline to FLAC's autoencoder compression pipeline. Together, Papers 1 and 2 establish a unified research theme: **training-signal-driven adaptive compression for communication-efficient FL**, which becomes the foundation for the full MAB formulation in Paper 3.

---

## 2. Research Context and Lab Lineage

### 2.1 FLAC's Position in Prof. Lu's Research Thread

FLAC is the strongest theoretical contribution in the lab's FL compression work. It is the only paper in the thread with a formal convergence proof. Understanding its architecture is essential before proposing an extension.

**FLAC's two-state operation:**

```
Training State (q rounds):
  → Clients send full uncompressed updates
  → Server trains autoencoder on received models
  → Check convergence condition: E[||e_i||²] ≤ eth[t] ~ O(1/t²)
  → If satisfied → switch to Compression State
  → If not → add more clients to AC or reduce C

Compression State:
  → Clients use encoder to compress local models
  → AC clients still send full uncompressed updates
  → Server uses AC updates + decoder to reconstruct all clients
  → Check condition every q rounds → re-enter Training State if violated
```

**The AC mechanism (FLAC Equation 6–7):**

```
w_de^i[t] = f_de(w_en^i[t], w_ac[t])     for i ∈ K \ AC
ŵ[t+1] = (1/ΣD_i) · (Σ_{i∉AC} D_i·w_de^i[t] + Σ_{i∈AC} D_i·w^i[t])
```

The decoder receives both the compressed update AND the uncompressed accurate users' models — the AC subset is what makes high-ratio compression possible while maintaining reconstruction quality.

### 2.2 What FLAC Left Open

FLAC's paper is explicit about its limitations. The following gaps are stated directly or implied by design choices:

| # | Limitation | Where It Appears | Severity |
|---|---|---|---|
| 1 | **AC subset is static and arbitrary** | Algorithm 1, line 9: "Add users to AC" — no policy given | 🔴 Critical |
| 2 | **i.i.d. experiments only** | Section V: "10 users with i.i.d. data distribution" | 🔴 Critical |
| 3 | **Full device participation assumed** | Theorem IV.1: "full device participation" | 🟡 Significant |
| 4 | **Single autoencoder for all clients** | Architecture description, Section IV | 🟡 Significant |
| 5 | **Static compression rate C** | Algorithm 1: C is fixed per state | 🟡 Significant |
| 6 | **No heterogeneous communication capacity** | No per-client C_i modeled | 🟡 Significant |

**DynAC-FLAC attacks Limitations 1 and 2** — the two most critical, and the two that have never been addressed in the literature.

### 2.3 The Publication Gap

After a thorough literature search, the finding is unambiguous: **no paper has directly extended FLAC since 2022.** It is cited in surveys as a benchmark but treated as a closed contribution. This is the same 2+ year gap identified for DSFL — and for the same reason: Mahdi left for industry and the thread stopped. DynAC-FLAC restarts it with the same logic ADAFL used for DSFL.

---

## 3. Problem Statement

### 3.1 The Static AC Problem

In FLAC's Algorithm 1, when the autoencoder fails to satisfy the convergence conditions, the server's response is:

```
Add users to AC   OR   decrease C
```

There is no policy for which users to add. The paper implicitly assumes random or manual selection. This creates three concrete failure modes in practice:

**Failure Mode 1 — Redundant AC selection:** If the server adds clients whose updates are already well-represented by the autoencoder (high similarity to global model, low unique information), the AC cost is wasted. These clients are sending full uncompressed updates even though compressed versions would reconstruct equally well.

**Failure Mode 2 — Missing critical AC members:** Clients with highly unique, non-i.i.d. data distributions are precisely the ones the autoencoder struggles to reconstruct — but a static/random AC policy may never include them. The autoencoder continues to fail on these clients silently.

**Failure Mode 3 — Stale AC membership:** A client's update informativeness changes over training. Early rounds, all clients are informative. Late rounds, most clients have converged and only a few are still making meaningful progress. A static AC policy wastes communication budget on converged clients.

### 3.2 The Non-IID Gap

FLAC's theorem covers non-i.i.d. data (Γ quantifies the degree of non-i.i.d. distribution in their Theorem IV.1 via the term 6LΓ). But every experiment in the paper uses i.i.d. data. This is not just a missing experiment — it reveals a fundamental limitation: FLAC's single shared autoencoder is trained to reconstruct an average of client updates, which is a reasonable approach for i.i.d. data but increasingly poor as data heterogeneity grows. Under extreme non-i.i.d. (e.g. Dirichlet α = 0.1), the autoencoder's average reconstruction fails catastrophically for outlier clients.

### 3.3 One-Sentence Formulation

> **FLAC selects accurate users arbitrarily and never tests non-i.i.d. settings — we introduce a dynamic, loss-feedback driven AC selection policy that targets clients the autoencoder reconstructs poorly, and prove it preserves FLAC's O(1/T) convergence guarantee under non-i.i.d. data.**

---

## 4. Proposed Method: DynAC-FLAC

### 4.1 The Two-Signal AC Selection Policy

DynAC-FLAC selects AC members each round using two signals already computed inside FLAC's pipeline — no additional communication overhead:

**Signal 1 — Reconstruction Error per client:**
```
r_i[t] = ||w^i[t] - w_de^i[t]||²
```
This is FLAC's existing error term e^i[t] from Equation 8, computed at the server after each Compression State round. High r_i means the autoencoder reconstructs client i poorly — client i carries unique information the encoder hasn't learned.

**Signal 2 — Loss Improvement Rate (from ADAFL):**
```
ΔL_i[t] = L_i[t-1] - L_i[t]
```
Clients with high ΔL_i are making rapid progress — their updates are currently informative and diverging from the average. These are exactly the clients whose full updates are most valuable for the autoencoder to see.

**The DynAC Selection Rule:**
```
Score_i[t] = α · r_i[t] + (1-α) · ΔL_i[t]

AC[t] = top-m clients by Score_i[t]
```
where m = |AC| is a budget parameter (number of accurate users per round), and α ∈ [0,1] balances reconstruction quality versus training progress as selection criteria.

**Intuition:** A client that the autoencoder reconstructs poorly AND is making rapid progress is the ideal AC candidate — its update is both unique and currently important. A client that is well-reconstructed and has converged can be compressed without loss.

### 4.2 Integration with FLAC's Algorithm

DynAC-FLAC modifies FLAC's Algorithm 1 at exactly one decision point — the AC membership update:

| FLAC Step | FLAC Original | DynAC-FLAC Modification |
|---|---|---|
| Algorithm 1, line 9 | "Add users to AC or decrease C" — no policy | `AC[t] = top-m{α·r_i[t] + (1-α)·ΔL_i[t]}` |
| Lines 15–17 | Compression State with fixed AC | Compression State with dynamic AC[t] |
| Condition check | Fixed AC for all q rounds | AC recomputed every round using Score_i[t] |
| All other steps | Unchanged | Unchanged |

Everything else — the autoencoder architecture, the Training/Compression State switching, the convergence condition checking, the aggregation rule — remains identical to FLAC.

### 4.3 Algorithm Pseudocode

```
DynAC-FLAC Algorithm:

Parameters: window q, compression rate C, AC budget m, 
            weighting α, loss threshold τ, eth

Initialization: State ← Training State, AC[0] ← random m clients

For each global round t:

  If State = Training State:
    For q iterations:
      All clients send full w^i[t] (no compression)
    Train autoencoder using Equation (5)
    Compute r_i[t] = ||w^i[t] - w_de^i[t]||² for all i
    If convergence conditions (9) satisfied:
      State ← Compression State
    Else:
      Continue Training State

  If State = Compression State:
    // DynAC Selection (NEW)
    For each client i:
      Score_i[t] = α · r_i[t] + (1-α) · ΔL_i[t]
    AC[t] = indices of top-m clients by Score_i[t]
    
    // Compression (same as FLAC)
    For i ∈ AC[t]:
      Send full w^i[t] to server
    For i ∉ AC[t]:
      Encode: w_en^i[t] = f_en(w^i[t])
      Send compressed w_en^i[t] to server
    
    Server reconstructs using Equation (6): 
      w_de^i[t] = f_de(w_en^i[t], w_ac[t])
    
    Update global model using Equation (7)
    
    Check condition (9) using last q rounds:
      If violated: State ← Training State
```

### 4.4 Why This Preserves FLAC's Convergence

FLAC's Theorem IV.1 requires two conditions on the autoencoder error:

```
(i)  E[||e^i||²] ≤ eth[t] ≤ η²_t ~ O(1/t²)    ∀i ∈ K
(ii) E[e^i] = 0                                   ∀i ∈ K
```

**Claim:** DynAC-FLAC's selection policy maintains both conditions.

For condition (i): DynAC prioritizes clients with high r_i (high reconstruction error) for AC inclusion. By including these clients uncompressed, the autoencoder receives the updates it struggles with most, directly reducing the maximum reconstruction error. The remaining compressed clients have low r_i by construction — their errors are already below threshold.

For condition (ii): The zero-mean condition is maintained because the policy is symmetric across rounds — no systematic bias is introduced by the scoring function. Clients are selected based on their current training state, not a fixed characteristic.

**Full proof:** Deferred to journal extension. For INFOCOM, a sketch of the argument above with reference to FLAC's existing proof structure is sufficient.

---

## 5. Related Work and Differentiation

### 5.1 What Exists Around FLAC

| Prior Work | Venue | What It Does | How DynAC-FLAC Differs |
|---|---|---|---|
| **FLAC (Beitollahi & Lu)** | Globecom 2022 | Autoencoder compression + convergence; static AC | We make AC selection dynamic and principled |
| **DSFL (Beitollahi et al.)** | ICCSPA 2022 | CKA-based sparsification; no autoencoder | Different compression mechanism; no AC concept |
| **FedLUAR** | arXiv 2025 | Server-side layer recycling | Recycling ≠ accurate user selection; no autoencoder |
| **Client selection for FL convergence** | Various | UCB/bandit client selection for training speed | Selection for convergence speed ≠ selection for reconstruction quality |
| **Personalized autoencoders** | Various | Per-client autoencoder | Different architecture; no principled AC; no convergence proof |
| **AdapComFL** | 2024 | Bandwidth-driven adaptive compression | Bandwidth signal only; no reconstruction error signal; no AC |
| **FedAvg with partial participation** | McMahan et al. | Random client selection | Random ≠ principled reconstruction-aware selection |

### 5.2 The Confirmed Unique Gap

No paper has proposed a **principled policy for AC subset selection in autoencoder-based FL compression** that:
- Uses reconstruction error as a selection signal
- Combines it with training progress (ΔL)
- Preserves the host paper's (FLAC's) convergence guarantee
- Addresses non-i.i.d. settings where autoencoder reconstruction is most challenging

This gap exists because nobody has extended FLAC at all since 2022. DynAC-FLAC is the first paper to do so.

---

## 6. Connection to ADAFL (Paper 1)

This is the key intellectual contribution of the two-paper arc: **the same ΔL_i[t] signal does different things in different compression architectures.**

| Signal | In ADAFL (Paper 1) | In DynAC-FLAC (Paper 2) |
|---|---|---|
| `ΔL_i[t]` | Drives adaptive K selection (how much to sparsify) | Drives AC membership (who sends uncompressed) |
| `r_i[t]` | N/A (DSFL has no reconstruction error) | Reconstruction error — who the autoencoder fails on |
| Combined signal | Single feedback rule for K | Weighted scoring for AC selection |
| Zero overhead | ✅ Uses existing loss computation | ✅ Uses existing reconstruction error + loss |
| Theory anchor | DSFL convergence conditions | FLAC Theorem IV.1 |

**The unified narrative for both papers:**

> *"In both sparsification-based (DSFL/ADAFL) and autoencoder-based (FLAC/DynAC-FLAC) FL compression, the training signal already present in the pipeline is sufficient to drive adaptive compression decisions — no additional communication, no external bandwidth estimation, no separate model required."*

This becomes the abstract of Paper 3 (the unified framework).

---

## 7. Experimental Plan

### 7.1 Datasets

| Dataset | Role | Why |
|---|---|---|
| **MNIST** | Baseline | Matches FLAC's original experiments — direct comparison |
| **CIFAR-10** | Standard benchmark | Also in FLAC's original paper — direct comparison |
| **UCI HAR** | Heterogeneity showcase | Natural non-i.i.d. per user — also used in ADAFL for continuity |
| **FEMNIST** | Extreme non-i.i.d. | High per-user heterogeneity — stress test for DynAC policy |

FEMNIST is important here because it's the standard extreme non-i.i.d. benchmark in FL. FLAC never tested it. Showing DynAC-FLAC works well on FEMNIST while static-AC FLAC fails is your strongest result.

### 7.2 Non-IID Partitioning

- **Dirichlet-α** partitioning with α ∈ {0.1, 0.5, 1.0} — sweep heterogeneity severity
- **Natural partitioning** for UCI HAR and FEMNIST (user-based)
- Compare directly to FLAC under same non-i.i.d. settings (FLAC never reported these — you fill the gap)

### 7.3 Baselines

| Baseline | Why Included |
|---|---|
| FedAvg (no compression) | Upper bound on accuracy |
| FLAC (static AC, random selection) | Direct predecessor — primary comparison |
| FLAC (static AC, worst-case selection) | Lower bound — shows AC selection matters |
| FLAC (static AC, oracle selection) | Upper bound — shows how good DynAC can theoretically get |
| DynAC-FLAC α=1.0 (reconstruction only) | Ablation — reconstruction signal alone |
| DynAC-FLAC α=0.0 (loss signal only) | Ablation — ADAFL signal alone |
| **DynAC-FLAC α=0.5 (combined)** | **Proposed method** |

The oracle baseline is particularly powerful — it shows the maximum possible gain from principled AC selection, making reviewers understand the ceiling you're approaching.

### 7.4 Metrics

- Test accuracy vs. communication round
- Test accuracy vs. total bits transmitted (uplink only — same as FLAC)
- Autoencoder reconstruction error over rounds (shows DynAC reduces it)
- AC membership churn over rounds (shows the policy adapts meaningfully)
- Convergence round to target accuracy under various non-i.i.d. settings

### 7.5 Key Experiment

**The money plot:** Side-by-side comparison of FLAC vs. DynAC-FLAC under Dirichlet α = 0.1 (extreme non-i.i.d.) showing:
- FLAC's accuracy degrades significantly as heterogeneity increases
- DynAC-FLAC maintains close to full-participation accuracy
- AC membership dynamically shifts to the most heterogeneous clients over rounds

This single figure tells the entire story of the paper.

---

## 8. Theoretical Positioning

### For INFOCOM (9 pages — full paper format):

INFOCOM expects stronger theory than Globecom. The theoretical contribution is:

**Proposition:** Under FLAC's existing assumptions (L-smooth, μ-strongly convex, bounded gradient variance), DynAC-FLAC's selection policy satisfies the error conditions required by Theorem IV.1, achieving the same O(1/T) convergence rate as FLAC with static AC.

**Proof sketch:** 
1. DynAC selects top-m clients by reconstruction error — by construction, the remaining compressed clients have reconstruction error below the median
2. The scoring function is bounded and depends only on quantities already bounded in FLAC's proof (e^i[t] and gradient norms)
3. The zero-mean condition holds because the selection is based on magnitude, not sign, of the error — no systematic bias is introduced

**Full proof:** Provided in the paper appendix (INFOCOM allows supplementary material).

### For Journal (ToN / TNNLS — if INFOCOM misses):

Add the full convergence proof with explicit tracking of how DynAC's dynamic AC membership affects the B term in FLAC's Theorem IV.1:

```
B = Σσ²_i/N² + 6LΓ + 8(E-1)G² + E[||eth||²]
```

Show that DynAC reduces the effective Γ (non-i.i.d. degree) by ensuring the most heterogeneous clients' updates are always accurately represented in aggregation.

---

## 9. 30-Day Execution Plan

| Days | Focus | Deliverable |
|---|---|---|
| **1–3** | Read FLAC carefully. Map all variables in Algorithm 1 to code in `github.com/mahdibeit/FLAC`. Understand autoencoder architecture. | Code map document |
| **4–6** | Implement DynAC selection module. Compute r_i[t] from existing FLAC reconstruction. Add ΔL_i tracking (reuse ADAFL code). | DynAC module (working) |
| **7–10** | Run FLAC baseline + DynAC on MNIST i.i.d. (reproduce FLAC results first, then show DynAC improvement). | MNIST results |
| **11–14** | Run on CIFAR-10 non-i.i.d. (Dirichlet α=0.1). This is the key experiment — FLAC never did this. | Core non-i.i.d. result |
| **15–18** | Add UCI HAR and FEMNIST. Run full ablation (α sweep). Generate oracle baseline. | Full experiment suite |
| **19–22** | Write full paper draft. INFOCOM format (9 pages): intro, related work, system model, algorithm, theory sketch, experiments, conclusion. | Complete draft |
| **23–26** | Revise. Add AC membership churn visualization. Tighten theory section. | Revised draft |
| **27–30** | Polish. Internal review with Prof. Lu feedback incorporated. Format. | Submission-ready PDF |

**Submission target:** INFOCOM 2027 (July 24, 2026) — this 30-day plan runs April–May 2026, leaving ~2 months for revision and strengthening before the deadline.

---

## 10. The Three-Paper Research Arc

This proposal is Paper 2 of a coherent three-paper research program. Each paper is publishable independently, but together they tell a single story.

```
┌─────────────────────────────────────────────────────────────────┐
│  PAPER 1: ADAFL (Globecom 2026)                                 │
│  → ΔL feedback drives adaptive K in DSFL's sparsification       │
│  → Venue: Globecom 2026 | Deadline: April 1, 2026               │
└────────────────────────┬────────────────────────────────────────┘
                         │ Same ΔL signal, different architecture
┌────────────────────────▼────────────────────────────────────────┐
│  PAPER 2: DynAC-FLAC (INFOCOM 2027)                             │
│  → ΔL + reconstruction error drives AC selection in FLAC        │
│  → Venue: INFOCOM 2027 | Deadline: July 24, 2026                │
└────────────────────────┬────────────────────────────────────────┘
                         │ Unified signal + MAB formalization
┌────────────────────────▼────────────────────────────────────────┐
│  PAPER 3: Unified MAB Framework (IEEE JSAC / NeurIPS 2027)      │
│  → Replace feedback rules in Papers 1+2 with UCB bandits        │
│  → Joint scheduling (HeteRo-Select) + compression (ADAFL/DynAC) │
│  → Full regret + convergence guarantees                         │
│  → Venue: IEEE JSAC or NeurIPS 2027+                            │
└─────────────────────────────────────────────────────────────────┘
```

**For Prof. Lu:** Papers 1 and 2 restart both of his dormant FL threads (DSFL and FLAC) using the same signal, and Paper 3 unifies them with Steiger's bandit framework. This is a complete 2-year research program for an M.A.Sc., with a natural PhD continuation.

---

## 11. Connection to Prof. Ning Lu's Research Agenda

| Prof. Lu's Paper | How DynAC-FLAC Connects |
|---|---|
| **FLAC (Globecom 2022)** | Direct extension — same codebase, same theoretical framework, fills FLAC's stated gap |
| **DSFL (ICCSPA 2022)** | ΔL signal reused from ADAFL — same feedback mechanism applied to different architecture |
| **POSS (INFOCOM 2023)** | AC selection as a constrained optimization problem → natural MAB extension in Paper 3 |
| **Backlogged Bandits (INFOCOM 2024)** | AC membership churn across rounds → backlogged selection policy in Paper 3 |

DynAC-FLAC is not an external idea brought into the lab — it is the natural second chapter of work Prof. Lu's own students started and left unfinished.

---

## 12. Available Resources

- **FLAC codebase** — Mahdi Beitollahi, public GitHub (`github.com/mahdibeit/FLAC`); full autoencoder + Training/Compression State implementation using Flower framework
- **ADAFL codebase** — Paper 1 implementation; ΔL computation module directly reusable
- **DSFL codebase** — `github.com/mahdibeit/DSFL`; non-i.i.d. simulation utilities
- **HeteRo-Select codebase** — natural non-i.i.d. partitioning for UCI HAR and FEMNIST
- **FLAC Theorem IV.1** — theoretical scaffold; DynAC proof is a structured extension, not a new derivation
- **Steiger's POSS + Backlogged Bandits + ToN 2026** — bandit theory scaffold for Paper 3
- **Slivkins MAB textbook** — being read in parallel; informs Paper 3 UCB formulation

---

## 13. Summary

| Item | Detail |
|---|---|
| **Working title** | DynAC-FLAC: Loss-Driven Accurate User Selection for Communication-Efficient FL |
| **Core idea** | Replace FLAC's static arbitrary AC selection with a dynamic policy using reconstruction error + loss feedback |
| **Gap attacked** | FLAC's AC selection is unprincipled + FLAC never tested non-i.i.d. settings |
| **Zero overhead** | Both signals (r_i and ΔL_i) already computed in FLAC's pipeline |
| **Theory** | Preserves FLAC's O(1/T) convergence — no new proof, just verification |
| **Primary target** | INFOCOM 2027 (A*, July 24, 2026) |
| **Backup target** | IEEE Transactions on Networking (rolling) |
| **Datasets** | MNIST, CIFAR-10, UCI HAR, FEMNIST |
| **Timeline** | 30 days (April–May 2026) |
| **Prerequisite** | ADAFL (Paper 1) submitted — ΔL module reused directly |
| **Position in arc** | Paper 2 of 3 — bridges ADAFL and the unified MAB framework |

---

*Prepared by Md Akmol Masud | March 2026 | Queen's University ECE — Incoming M.A.Sc. September 2026*  
*Part of a three-paper research arc: ADAFL (Globecom 2026) → DynAC-FLAC (INFOCOM 2027) → Unified MAB Framework (JSAC/NeurIPS 2027+)*
