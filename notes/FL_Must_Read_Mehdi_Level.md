# FL Must-Read List — Mehdi-Level Depth (No Excuse)

**Goal:** Deep primary expertise in **Federated Learning** so you can hold your own in Dr. Lu's lab and speak Mehdi Beitollahi's language. Secondary: RL + networks.

**What you already have:** IoTJ survey (Beitollahi–Lu) ✅; Layer Normalization in Label-Skewed FL (Zhang et al., TMLR 2024) ✅ with full notes. That gives you non-IID/label-shift theory and FL notation.

**What “Mehdi-level” means:** (1) **Foundations** — FedAvg, convergence language, why FL is hard. (2) **Theory** — convergence under heterogeneity, variance reduction, personalization. (3) **Communication efficiency** — compression, sparsification (FLAC, DSFL). (4) **Lu-lab + Mehdi’s own work** — so you can extend it and cite correctly.

---

## If your focus is **non-IID** — how the list changes

For **FL on non-IID data** (statistical heterogeneity, client drift, label shift), **reorder and add** as below. The core non-IID canon is: **FedAvg** (baseline) → **FedProx** (why non-IID hurts + proximal fix) → **SCAFFOLD** (client drift + control variates) → **Layer Norm** (label shift; you're done) → convergence analysis of FedAvg under heterogeneity.

### Non-IID Tier 1 (must read, in this order)

| # | Paper | Why for non-IID |
|---|--------|------------------|
| 1 | **FedAvg** (McMahan et al. AISTATS 2017) | Baseline; non-IID is where FedAvg degrades. Same as main list. |
| 2 | **FedProx** (Li et al. MLSys 2020) | **Convergence under heterogeneity**; local dissimilarity; proximal term to limit client drift. [arXiv:1812.06127] |
| 3 | **SCAFFOLD** (Karimireddy et al. ICML 2020) | **Client drift**; control variates to correct local updates; variance reduction for non-IID. |
| 4 | **Li et al.** "On the Convergence of FedAvg on Non-IID Data" (ICLR 2020) or equivalent FedAvg convergence analysis | **Formalizes** why non-IID hurts (bounded variance, client sampling, convergence rate). |
| 5 | **Layer Norm in label-skewed FL** (Zhang et al. TMLR 2024) | ✅ **Done** — label shift, feature collapse, normalization. |
| 6 | **FLAC** (Beitollahi, Lu, GLOBECOM 2022) | Lu-lab; convergence **with non-IID** in the proof; compression. |
| 7 | **DSFL** (Beitollahi, Liu, Lu, ICCSPA 2022) | Lu-lab; communication efficiency; often used with non-IID setups. |
| 8 | **Li et al. survey** (Challenges, Methods, Future Directions) | Vocabulary: statistical vs system heterogeneity, client drift, taxonomy. |

**Add to Tier 2 for non-IID:** **FedDC** (Gao et al., CVPR 2022 — local drift decoupling); **FedDyn** (dynamic regularization for heterogeneity); **heavy-tailed gradients** (for your proposal). Optional: a **systematic review / taxonomy** of non-IID in FL.

**Summary:** For non-IID focus, read **FedAvg → FedProx → SCAFFOLD → FedAvg convergence (non-IID)** first, then Layer Norm (done), then FLAC/DSFL and the survey. That gives you the **theory of heterogeneity** before Lu-lab systems.

---

## Tier 1 — Must read (no excuse)

These are the **minimum** to claim deep FL. Order = reading order.

| # | Paper | Venue | Why you must read it |
|---|--------|--------|----------------------|
| **1** | **McMahan, Moore, Ramage, Hampson, Arcas.** "Communication-Efficient Learning of Deep Networks from Decentralized Data." | AISTATS 2017 | **FedAvg.** The baseline every FL paper compares to. You need the algorithm (local SGD + weighted averaging), the notation, and the empirical setup. No FedAvg = no FL literacy. [arXiv:1602.05629] |
| **2** | **Beitollahi, Lu.** "FLAC: Federated Learning with Autoencoder Compression and Convergence Guarantee." | GLOBECOM 2022 | **Lu-lab core.** Compression via autoencoder; **convergence proof** (strongly convex, non-IID). Your proposal couples scheduling with FLAC. You have the PDF. **Conference paper — short.** |
| **3** | **Beitollahi, Liu, Lu.** "DSFL: Dynamic Sparsification for Federated Learning." | ICCSPA 2022 | **Lu-lab core.** Per-client dynamic sparsification (LSS + top-K). Communication-efficient FL; often cited with FLAC. **Conference paper — short.** |
| **4** | **Li, Sahu, Talwalkar, Smith.** "Federated Learning: Challenges, Methods, and Future Directions." | IEEE Signal Processing Magazine 2020 (or the longer survey) | **Canonical survey** on heterogeneity, convergence, personalization, and open problems. Gets you the vocabulary (statistical heterogeneity, client drift, FedProx/SCAFFOLD in one place). |
| **5** | **Li et al.** "Federated Optimization in Heterogeneous Networks" (FedProx). | MLSys 2020 | **Convergence under heterogeneity.** FedProx = FedAvg + proximal term; first-rate convergence analysis with local dissimilarity. You need this for “why non-IID hurts and how people fix it.” [arXiv:1812.06127] |
| **6** | **Karimireddy et al.** "SCAFFOLD: Stochastic Controlled Averaging for Federated Learning." | ICML 2020 | **Variance reduction for non-IID.** Control variates to correct client drift. Theory + experiments. Everyone doing serious FL theory knows FedAvg, FedProx, SCAFFOLD. |
| **7** | **Zhang et al. (Beitollahi).** "Understanding the Role of Layer Normalization in Label-Skewed Federated Learning." | TMLR 2024 | ✅ **Done** — you have full notes. This is your **theory depth** on label shift + normalization. |
| **8** | **Beitollahi et al.** "One-Shot Federated Learning with Foundation Models" (FedPFT). | TMLR 2025 | **Mehdi’s FL + foundation models.** Parametric feature transfer, one-shot FL. You have the PDF. Read after 1–6 so you see how it sits on top of classic FL. |

**Summary Tier 1:** FedAvg (1) → FLAC (2) → DSFL (3) → Li survey (4) → FedProx (5) → SCAFFOLD (6) → Layer Norm done (7) → FedPFT (8). That’s **8 papers** (7 to read; 1 done). No excuse to skip 1, 2, 3, 5, 6 if you want Mehdi-level depth.

---

## Tier 2 — Should read (theory + systems depth)

| # | Paper | Why |
|---|--------|-----|
| **9** | **Konečný et al.** "Federated Learning: Strategies for Improving Communication Efficiency." (Google) | Structured/sketched updates; compression. Pairs with FLAC/DSFL. |
| **10** | **Li et al.** "On the Convergence of FedAvg on Non-IID Data." (ICLR 2020 or similar) | FedAvg convergence analysis; bounded variance, client sampling. **Non-IID focus: Tier 1.** |
| **11** | **Mehdi’s Federated Unlearning (NoT).** CVPR 2025 | You have PDF. Unlearning in FL; good for breadth and citing Mehdi. |
| **12** | **Yang et al.** or similar on **heavy-tailed gradients** in FL | Directly supports your proposal (heavy-tailed client rewards). Search: "heavy tail federated learning gradient." |
| **13** | **FedDC** (Gao et al., CVPR 2022) — local drift decoupling for non-IID | Local drift variable; decouples client-specific drift from global model. |
| **14** | **FedDyn** — dynamic regularization for heterogeneous FL | Dynamic regularizer to reduce client drift under heterogeneity. |

---

## Tier 3 — Good to have (broader FL)

- **Personalization:** Per-FedAvg, pFedHN, or FedRep — when you need “personalized FL” in related work.
- **Byzantine / robustness:** If you touch robust aggregation (your proposal mentions geometric median, TailOPT).
- **Decentralized FL:** If you go toward gossip / serverless (e.g. Mehdi’s DFML).

---

## Checklist (track yourself)

- [ ] **FedAvg** (McMahan et al. AISTATS 2017)
- [ ] **FLAC** (Beitollahi, Lu, GLOBECOM 2022)
- [ ] **DSFL** (Beitollahi, Liu, Lu, ICCSPA 2022)
- [ ] **Li et al. survey** (challenges, methods, future directions)
- [ ] **FedProx** (Li et al. MLSys 2020)
- [ ] **SCAFFOLD** (Karimireddy et al. ICML 2020)
- [x] **Layer Norm label-skewed FL** (Zhang et al. TMLR 2024) — notes in `02_Layer_Normalization_Label_Shift_FL.md`
- [ ] **FedPFT** (One-shot FL with foundation models, TMLR 2025)

After Tier 1 you will have: **foundations (FedAvg, FedProx, SCAFFOLD), Lu-lab communication efficiency (FLAC, DSFL), and Mehdi’s theory + modern FL (Layer Norm, FedPFT).** That’s the no-excuse core for being the “FL guy” in Lu’s lab with strong secondary RL and networks.


### Non-IID focus — checklist

- [ ] FedAvg
- [ ] FedProx
- [ ] SCAFFOLD
- [ ] FedAvg convergence on non-IID (Li et al. or equivalent)
- [x] Layer Norm label-skewed FL
- [ ] FLAC
- [ ] DSFL
- [ ] Li survey
- [ ] (Tier 2) FedDC, FedDyn, heavy-tailed gradients


---

*Add notes under `notes/` as you go (e.g. `03_FLAC.md`, `04_FedAvg.md`).*
