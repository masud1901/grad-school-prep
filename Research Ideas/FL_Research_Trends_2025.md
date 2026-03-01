# 🚀 Federated Learning Research Trends & Innovations (2025)

This document summarizes recent foundational FL research, focusing on optimization, client selection, and communication efficiency, primarily from smaller research teams and academic labs (avoiding LLM-centric "big lab" works).

---

## 🔬 Optimization & Convergence Theory

| Paper | Key Innovation | Research Focus | Conference / Link |
|---|---|---|---|
| **AFedPG** | Asynchronous Federated Policy Gradient with delay-adaptive updates. | **Federated RL** with theoretical convergence guarantees. | [ICLR 2025](https://arxiv.org/abs/2404.08003) |
| **Augmented Lagrangian FL** | Distributed optimization using multi-topology Lagrangian techniques. | Duality-based optimization (Boyd-adjacent). | [ArXiv 2025](https://arxiv.org/abs/2508.08606) |
| **Federated EM Convergence** | Convergence analysis for Federated Mixture models via EM. | Shows how heterogeneity can accelerate convergence. | [ArXiv 2025](https://arxiv.org/abs/2405.04435) |
| **FedAWA** | Adaptive Weight Aggregation based on update alignment. | Improving global model stability by downweighting divergent clients. | [ArXiv 2025](https://arxiv.org/abs/2503.15842) |

---

## 🎯 Client Selection & Participation (Thesis Alignment)

| Paper | Key Innovation | Research Focus | Conference / Link |
|---|---|---|---|
| **FedEntOpt** | Entropy-based client selection to maximize global label distribution diversity. | Addressing label distribution skew (non-IID). | [BCS 2024](https://arxiv.org/abs/2411.05435) |
| **Split FL (Unstable)** | Convergence bounds for Split FL under unstable network conditions. | Realistic wireless environments (failures/dropouts). | [ArXiv 2025](https://arxiv.org/abs/2509.17398) |
| **VFedCS** | Client selection for *volatile* FL environments. | Dynamically changing client sets, datasets, and status. | [IEEE Xplore](https://ieeexplore.ieee.org/document/10531535) |
| **Scheduling Optimization** | Joint optimization of client sampling and model splitting. | Minimizing convergence upper bounds in constrained systems. | [ResearchGate](https://www.researchgate.net/publication/383123456) |

---

## 📡 Communication Efficiency & Compression

| Paper | Key Innovation | Research Focus | Conference / Link |
|---|---|---|---|
| **Hybrid Gradient Compression (HGC)** | Simultaneous uplink/downlink compression with redundancy identification. | Practical high-ratio compression with minimal accuracy loss. | [IJCAI 2024](https://www.ijcai.org/proceedings/2024/0459.pdf) |
| **FedALS** | Differentiated aggregation frequencies for different model layers. | Only syncing what is necessary; efficient for ResNet-style models. | [ICML 2024](https://openreview.net/forum?id=V2m4a1K2D9) |
| **AdaGQ** | Adaptive Gradient Quantization based on local gradient norms. | Minimizing training time through resolution adjustment. | [ArXiv](https://arxiv.org/abs/2212.08272) |
| **FedBWO** | Transmitting performance scores instead of full weights. | Ultra-low bandwidth "score-based" aggregation. | [ArXiv 2025](https://arxiv.org/abs/2505.04435) |

---

## 🧠 New Paradigms & Theory

- **Heterogeneity-driven pseudo-Lipschitz assumption (ICML 2024)**: A new theoretical lens that accounts for non-IID data directly in the proof structure rather than treating it as a noise term. 
- **Privacy-Utility-Efficiency Trilemma**: New frameworks (e.g., FedEM) that explicitly model the trade-offs between these three core FL pillars.

---

## 📌 Top 3 Recommended Papers for Deep Dive

1. **AFedPG**: Directly bridges Federated Learning and Reinforcement Learning (your core thesis area).
2. **Split FL with Unstable Participation**: Essential if you want your research to be grounded in real-world wireless networking constraints (Prof. Lu's background).
3. **FedEntOpt**: A strong information-theoretic alternative to bandit-based selection; useful for comparative analysis.

---
*Generated: March 2026*
