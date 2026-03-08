# RESEARCH PROPOSAL

## Robust Client Scheduling for Federated Learning over Heterogeneous Wireless Networks

| | |
|---|---|
| **Applicant** | Md Akmol Masud |
| **Program** | M.A.Sc., Electrical & Computer Engineering, Queen's University |
| **Supervisor** | Dr. Ning Lu (Tier 2 Canada Research Chair, Future Communication Networks) |
| **Start Date** | September 2026 |
| **Keywords** | Federated Learning, Multi-Armed Bandits, Client Scheduling, Heterogeneous Networks, Robust Optimization |

---

## 1. Motivation and Problem Context

Federated learning (FL) has emerged as a foundational paradigm for training machine learning models across distributed edge devices without centralizing raw data. Its appeal lies in enabling privacy-preserving, collaborative learning at scale. However, the practical deployment of FL over real wireless networks exposes a critical mismatch: standard FL theory assumes well-behaved, light-tailed distributions for client gradient norms, response times, and data quality, yet empirical evidence from heterogeneous network environments consistently shows heavy-tailed, power-law behaviour in these very quantities.

This mismatch is not a minor modelling inconvenience. Heavy-tailed client behaviour means that conventional UCB-based bandit algorithms — used widely for client scheduling in FL — rely on confidence bounds derived under subgaussian assumptions that are invalid in this regime. Equally, the FedAvg aggregation step implicitly invokes a central-limit-theorem argument when averaging client gradients; under infinite or near-infinite variance gradient distributions, this averaging is statistically unreliable and convergence guarantees dissolve. The consequence is that a system that appears stable in expectation may experience catastrophic gradient explosion or severe straggler-induced delay from a small fraction of outlier clients.

My prior work, **HeteRo-Select**, took a first step toward robust client scheduling under extreme heterogeneity using a heuristic bandit-inspired selection strategy. That work surfaced a core limitation: the exploration policies underpinning UCB-style scheduling are designed for subgaussian reward distributions. When client utilities — gradient quality, communication latency, data representativeness — exhibit heavy tails, these policies over-explore uninformative clients and under-protect against catastrophic outliers. This proposal formalises this gap and proposes a tractable algorithmic solution grounded in Dr. Lu's established research programme.

---

## 2. Research Gap and Literature Context

The interplay between bandit-based client scheduling and heavy-tailed reward distributions in FL has not been systematically studied. The relevant literature divides into four threads, each addressing only part of the problem. The research gap lies precisely at their intersection.

### 2.1 Bandit-Based Client Scheduling in FL

Client scheduling in FL has been extensively studied as a combinatorial multi-armed bandit (CMAB) problem. Nishio and Yonetani (2019) introduced **FedCS**, one of the earliest deadline-aware client selection protocols for mobile edge computing, which greedily selects clients that can complete local training within a round deadline — but without any learning component. Xia et al. (2020) advanced this by modelling client utility as stochastic rewards and applying UCB-based policies (**CS-UCB**) to balance exploration and exploitation across heterogeneous clients in wireless FL, demonstrating convergence under a bounded-reward assumption.

More recent work has deepened the CMAB formulation. **EACS-FL** (2024) frames energy-aware client selection as a heterogeneous budget-limited CMAB problem and uses Lyapunov optimization to track energy queues while achieving sublinear regret. Liu et al. (2021) proposed importance- and latency-aware scheduling for low-latency wireless communications. Federated contextual combinatorial cascading bandits (FedC3UCB-H, AAAI 2023) further introduced asynchronous communication and heterogeneous user feedback into the bandit scheduling model.

Despite this progress, a critical assumption runs through all of these works: client reward distributions are treated as bounded or subgaussian, enabling standard UCB concentration inequalities (e.g., Hoeffding or Bernstein bounds) to yield valid confidence intervals. This assumption is adopted for theoretical convenience and has not been empirically validated against real heterogeneous wireless traces. As discussed in Section 2.3 below, it fails precisely in the regime that FL practitioners encounter in practice.

### 2.2 Heavy-Tailed Gradient Noise and Robust Aggregation in FL

A distinct body of work has established that **heavy-tailed gradient noise is an inherent feature** of federated learning with non-IID data — not an edge case. Yang et al. (2022) formally characterised gradient norm distributions across clients in non-IID FL settings, showing that power-law behaviour emerges naturally from data heterogeneity and that standard FedAvg aggregation may diverge when gradient variance is unbounded. The **TailOPT** framework (2025) proposes adaptive gradient clipping to handle this regime, establishing finite-time convergence under α-stable noise assumptions.

Earlier robust aggregation work by El Mhamdi et al. (2021, *"Robust Aggregation for Federated Learning"*, IEEE Transactions on Signal Processing) proposed a geometric median-based oracle at the aggregation step that is resilient to both corrupted updates and heavy outliers — providing convergence guarantees for additive models when corruption levels are high. Li et al. (2020) showed that FedAvg's convergence on non-IID data slows with increased data heterogeneity and requires decaying learning rates, implicitly motivating more selective scheduling.

**A critical gap remains**: all of these works operate at the gradient aggregation level and assume a fixed, arbitrary client population at each round. They do not address *which clients to select* before aggregation. A robust scheduling policy that upstream identifies and manages heavy-tail clients — rather than waiting to clip or discard their gradients downstream — is absent from the literature.

### 2.3 Heavy-Tailed Bandits: Robust Mean Estimation Theory

The statistics and bandit theory communities have developed principled tools for estimation under heavy-tailed distributions that the FL scheduling literature has not yet adopted. The foundational result by Bubeck, Cesa-Bianchi, and Lugosi (2013) showed that logarithmic regret in MABs is achievable with only a finite variance assumption, using **Median-of-Means (MoM)** to construct sub-Gaussian-like confidence intervals.

Catoni's M-estimator (2012, *Annals of Statistics*) provides a complementary approach: by minimising a robust influence function, it attains near-optimal mean squared error under a finite second-moment assumption, without requiring bounded support. Lugosi and Mendelson (2019, *Foundations of Computational Mathematics*) unified these estimators and established sharp sample complexity bounds for mean estimation of function classes under p-th moment assumptions (p ∈ (1, 2]).

For bandit algorithms specifically, Shao et al. (ICML 2022) combined Catoni's estimator with an optimism-based exploration policy to obtain an adaptive **"best-of-both-worlds"** UCB variant that achieves near-optimal regret under heavy tails and matches classical UCB under subgaussian rewards — demonstrating that robust estimation can be integrated into the bandit framework without sacrificing efficiency. Further, "Catoni-OFUL" (2023) extended this to contextual linear bandits with variance-weighted optimism, achieving regret bounds that grow with cumulative reward variance rather than worst-case reward range.

These results collectively provide the statistical machinery to extend UCB-style scheduling to the heavy-tailed regime. What is missing is their integration with a *constrained* bandit framework — specifically, one with switching costs and fairness guarantees as in Dr. Lu's POSS work.

### 2.4 Dr. Lu's Constrained Bandit Framework and the Extension Gap

Dr. Lu's **Pessimistic-Optimistic Slow Switching (POSS)** algorithm (INFOCOM 2023) addresses constrained bandits with switching costs, simultaneously achieving sublinear regret O(T^{3/4}) and vanishing constraint violation through a virtual-queue-based dual approach. The **Backlogged Bandits** work (INFOCOM 2024) extends this to multi-server queueing networks where arms represent service channels with backlogged demand, establishing a richer model for wireless scheduling with queue dynamics.

Both papers operate entirely under standard stochastic bandit assumptions — bounded or subgaussian rewards — which yield the sub-Gaussian concentration inequalities the virtual-queue updates depend upon. The question of what happens to regret and constraint violation guarantees when client rewards are drawn from heavy-tailed distributions (α-stable, infinite variance) is entirely open. Replacing the sample mean in POSS's optimistic index with a robust estimator (MoM or Catoni) modifies both the exploration bonus and the virtual queue update, potentially requiring a different block-length schedule or penalty structure to preserve the O(T^{3/4}) guarantee. This represents a **well-scoped, theoretically motivated, and practically consequential** open problem at the exact intersection of Dr. Lu's expertise and the limitations identified in Threads 2.1–2.3 above.

---

## 3. Proposed Research

### 3.1 Problem Formulation

We model FL client scheduling as a **constrained combinatorial multi-armed bandit with switching costs**, extending the POSS framework to the heavy-tailed reward regime. Formally, let there be N clients, each with unknown reward distribution P_k. At each FL round t, the server selects a subset S_t of clients subject to:

- An anytime cumulative **fairness constraint** (each client must be selected at least a minimum fraction of rounds, consistent with POSS).
- A **switching cost** incurred when the selected subset changes between rounds (reflecting real wireless scheduling overhead).
- The reward X_{k,t} for client k at round t follows a **heavy-tailed distribution** with tail index α_k < 2, so variance may be infinite.

The objective is to maximise cumulative network utility (a function of gradient quality, communication delay, and data diversity) while satisfying the fairness constraint and minimising switching costs, **without assuming subgaussian rewards**.

### 3.2 Core Technical Approach

The key algorithmic challenge is that UCB confidence bounds of the form `μ_k + sqrt(log t / n_k)` are only valid when rewards are subgaussian. Under heavy tails, the sample mean itself is an unreliable estimator. We propose replacing the sample mean within the POSS pessimistic-optimistic framework with a **robust mean estimator** that achieves good concentration even under heavy tails. Two concrete candidates are:

- **Median-of-Means (MoM)**: partition the n_k observations for arm k into blocks, compute block means, return the median. Achieves sub-Gaussian-like concentration without assuming finite variance.
- **Catoni's M-estimator**: minimises a robust influence function, achieving near-optimal mean square error under only a finite second-moment assumption.

Substituting either estimator into the POSS block-based structure modifies the optimistic index and the constraint tracking virtual queue update. The research question is whether the resulting algorithm retains POSS's sublinear regret guarantee O(T^{3/4}) and vanishing constraint violation, or whether the heavy-tail setting demands a modified block length schedule or a different penalty structure.

### 3.3 Connection to FLAC

Dr. Lu's FLAC system compresses client model updates via autoencoders before uplink transmission. Under heavy-tailed gradient distributions, the reconstruction error of the autoencoder has heavier tails than assumed in the FLAC convergence analysis. A secondary research thread will investigate whether the client scheduling policy developed here can be coupled with FLAC compression adaptively: clients identified as heavy-tail outliers by the scheduler can be assigned higher compression ratios — trading reconstruction fidelity for communication robustness — rather than simply excluded from the round.

### 3.4 Research Questions

- **RQ1**: Can the POSS constrained bandit framework be extended with a robust mean estimator to achieve sublinear regret under heavy-tailed client reward distributions, without sacrificing fairness constraint satisfaction?
- **RQ2**: What is the empirical distribution of client gradient norms and response times in heterogeneous wireless FL testbeds? Is the heavy-tail assumption empirically justified, and if so, what tail index is typical?
- **RQ3**: Can an adaptive coupling between the robust scheduler and the FLAC compression level improve end-to-end communication efficiency and model accuracy under heavy-tailed client conditions?

---

## 4. Methodology and Work Plan

| Phase | Activities |
|---|---|
| **Phase 1** (Months 1–4) | Literature review of heavy-tailed bandit algorithms (MoM, Catoni, robust UCB). Empirical characterisation of gradient norm and latency distributions using open FL benchmarks (LEAF, FedScale). Formalise problem as constrained heavy-tailed MAB. |
| **Phase 2** (Months 5–8) | Algorithm design: integrate robust mean estimator into POSS block structure. Theoretical analysis: derive regret and constraint violation bounds under α-stable reward assumption. Identify conditions under which sublinear regret is preserved. |
| **Phase 3** (Months 9–12) | Simulation experiments on heterogeneous FL testbed (varying N, α, switching cost λ). Comparison against UCB-POSS baseline, FedCS, and EACS-FL. Ablation on choice of robust estimator. |
| **Phase 4** (Months 13–16) | Adaptive coupling with FLAC compression. Prototype implementation. Write-up and submission to IEEE INFOCOM or IEEE Transactions on Networking. |

---

## 5. Expected Contributions

- A theoretical extension of the POSS constrained bandit framework to heavy-tailed reward distributions, with rigorous regret and constraint violation guarantees.
- Empirical characterisation of client distribution tails in realistic heterogeneous FL environments, establishing whether the heavy-tail assumption is necessary in practice.
- An adaptive client scheduling algorithm that is provably robust to power-law client behaviour, directly applicable to FL over 5G/6G wireless edge networks.
- A secondary result on coupling robust scheduling with adaptive compression (FLAC), contributing to Dr. Lu's communication-efficient FL research agenda.

---

## 6. Alignment with Dr. Lu's Research Programme

| Dr. Lu's Existing Work | This Proposal's Extension |
|---|---|
| POSS: Constrained bandits with switching costs (INFOCOM 2023) | Extend POSS to heavy-tailed reward regime using robust mean estimators |
| Backlogged Bandits: Queue-aware bandit scheduling (INFOCOM 2024) | Incorporate heavy-tailed service time distributions into backlog analysis |
| FLAC: Autoencoder compression for FL (GLOBECOM 2022) | Adaptive compression level as a function of scheduler's tail-risk assessment |
| Delayed Semi-Bandit Feedback (INFOCOM 2022) | Delayed feedback under heavy-tailed client latency distributions |

---

## 7. Applicant Background

My prior work provides direct grounding for this proposal. **HeteRo-Select** (arXiv 2025) demonstrates my ability to design and evaluate client selection algorithms for FL under extreme heterogeneity, having conceived the problem, implemented the algorithm, and run all experiments independently. Reviewers at IEEE TNNLS requested stronger theoretical guarantees, which directly motivated my interest in joining Dr. Lu's group to develop the principled bandit-theoretic framework proposed here.

My **Reinforcement Learning Specialization** (University of Alberta, Coursera) gives me working familiarity with multi-armed bandit theory — including UCB, Thompson Sampling, and regret analysis — which are the foundational tools this proposal builds upon. My publication record (6 papers including IEEE Transactions on Artificial Intelligence, Computers in Biology and Medicine, and Biomedical Signal Processing and Control) demonstrates the ability to produce work that survives rigorous peer review.

My experience at **Nimbus Research Bureau** deploying production analytics pipelines under real-world data constraints reinforces the practical motivation: the heavy-tail problem is not academic — it arises in every real networked system where client populations are diverse and uncontrolled.

---

## 8. Key References

### Dr. Lu's Framework (Direct Extensions)
1. Steiger, Li, Ji, Lu. "Constrained Bandit Learning with Switching Costs for Wireless Networks." IEEE INFOCOM 2023.
2. Steiger, Li, Lu. "Backlogged Bandits: Cost-Effective Learning for Utility Maximization in Queueing Networks." IEEE INFOCOM 2024.
3. Beitollahi, Lu. "FLAC: Federated Learning with Autoencoder Compression and Convergence Guarantee." IEEE GLOBECOM 2022.

### Applicant's Prior Work
4. Masud, Jahin, Hasan. "Stabilizing Federated Learning under Extreme Heterogeneity with HeteRo-Select." arXiv 2025.

### Bandit-Based FL Scheduling
5. Xia et al. "Multi-Armed Bandit Based Client Scheduling for Federated Learning." IEEE Transactions on Wireless Communications, 2020.
6. Nishio, Yonetani. "Client Selection for Federated Learning with Heterogeneous Resources in Mobile Edge." IEEE ICC 2019. *(FedCS)*
7. Liu et al. "Client Scheduling for Federated Learning with Importance and Latency Awareness." IEEE INFOCOM Workshop, 2021.
8. Li et al. "Energy-Aware Client Selection for Federated Learning via Heterogeneous Budget-Limited Combinatorial Bandits." *(EACS-FL)* 2024.

### Heavy-Tailed FL and Robust Aggregation
9. Yang et al. "Taming Fat-Tailed Gradient Noise in Federated Learning." arXiv 2022.
10. TailOPT. "Framework for Heavy-Tailed Distributed Optimization with Adaptive Clipping." arXiv 2025.
11. El Mhamdi et al. "Robust Aggregation for Federated Learning." IEEE Transactions on Signal Processing, 2021.
12. Li et al. "On the Convergence of FedAvg on Non-IID Data." ICLR 2020.

### Heavy-Tailed Bandit Theory and Robust Mean Estimation
13. Bubeck, Cesa-Bianchi, Lugosi. "Bandits with Heavy Tail." IEEE Transactions on Information Theory, 2013.
14. Catoni. "Challenging the Empirical Mean and Empirical Variance: A Deviation Study." Annales de l'Institut Henri Poincaré, 2012.
15. Lugosi, Mendelson. "Mean Estimation and Regression Under Heavy-Tailed Distributions." Foundations of Computational Mathematics, 2019.
16. Shao et al. "Almost Optimal Algorithms for Two-player Zero-Sum Linear Mixture Markov Games with Value Function Approximation." *(Includes Catoni-UCB for heavy-tailed MABs)* ICML 2022.
17. He et al. "Nearly Minimax Optimal Regret for Heavy-Tailed Bandits." NeurIPS 2022.
